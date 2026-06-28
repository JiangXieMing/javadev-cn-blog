---
title: "LangChain4j 入门实战：用Java构建你的第一个AI Agent"
date: 2026-06-28
draft: false
tags: ["langchain4j", "ai-agent", "java", "教程"]
description: "从零开始学习LangChain4j，构建一个能调用工具的AI Agent。涵盖AiService、Tool Calling、Memory等核心概念，附完整代码示例。"
---

LangChain4j是目前Java生态中最成熟的AI应用框架。本文手把手教你用LangChain4j构建一个能调用外部工具的AI Agent——不需要Python，不需要复杂的配置，纯Java搞定。

## 为什么选择LangChain4j？

如果你是Java开发者，想把AI能力集成到项目中，有几个选择：

- **直接调OpenAI API** — 可以，但要自己处理prompt管理、memory、tool calling
- **Spring AI** — Spring官方出品，但还比较新，Agent支持不完善
- **LangChain4j** — 最成熟，功能最全，社区最活跃

**选LangChain4j，理由很简单：开箱即用的AI Agent能力。**

## 5分钟搭建项目

### 1. 创建Spring Boot项目

```xml
<dependency>
    <groupId>dev.langchain4j</groupId>
    <artifactId>langchain4j-open-ai-spring-boot-starter</artifactId>
    <version>0.36.2</version>
</dependency>
```

### 2. 配置API Key

```yaml
langchain4j:
  open-ai:
    chat-model:
      model-name: gpt-4o-mini
      temperature: 0.3
```

环境变量：
```bash
export OPENAI_API_KEY=sk-your-key-here
```

### 3. 写第一个AI Service

```java
@AiService
public interface ChatAssistant {
    
    @SystemMessage("你是一个专业的Java技术顾问")
    String chat(@UserMessage String question);
}
```

**就这样。** LangChain4j在运行时自动生成实现类。你只需要注入使用：

```java
@RestController
public class ChatController {
    
    private final ChatAssistant assistant;
    
    @GetMapping("/chat")
    public String chat(@RequestParam String message) {
        return assistant.chat(message);
    }
}
```

## 核心：让Agent会"用工具"

AI Agent和普通聊天的区别就是**工具调用**。让AI不只是能说话，还能**做事**。

### 定义工具

```java
public class DatabaseTools {
    
    @Tool("根据订单号查询订单状态")
    public OrderStatus queryOrder(@P("orderId") String orderId) {
        return orderService.getOrderStatus(orderId);
    }
    
    @Tool("查询库存数量")
    public int checkStock(@P("productId") String productId) {
        return inventoryService.getStock(productId);
    }
    
    @Tool("取消订单")
    public String cancelOrder(@P("orderId") String orderId) {
        return orderService.cancel(orderId) ? "取消成功" : "取消失败";
    }
}
```

### 创建Agent

```java
@AiService
public interface CustomerServiceAgent {
    
    @SystemMessage("""
        你是一个电商客服AI助手。
        你可以使用以下工具来帮助客户：
        - 查询订单状态
        - 查询库存
        - 取消订单
        
        在回答问题时，先思考需要哪些信息，然后调用相应的工具。
        如果缺少必要信息，礼貌地向客户询问。
        """)
    String handleCustomerRequest(
        @UserMessage String request,
        @ToolSet DatabaseTools tools
    );
}
```

### 测试

```java
String response = agent.handleCustomerRequest(
    "帮我查一下订单202406280001的状态"
);
// AI会自动调用 queryOrder("202406280001") 
// 然后用查询结果生成回复
```

**AI的思考过程（自动完成，你不需要管）：**
```
1. 用户想查订单状态
2. 提取订单号：202406280001
3. 调用工具：queryOrder("202406280001")
4. 拿到结果：已发货，预计明天到达
5. 生成回复："您的订单202406280001已发货，预计明天到达。"
```

## 对话记忆

默认情况下，每次调用AI都是独立的。加上 `@MemoryId` 就能记住上下文：

```java
@AiService
public interface CustomerServiceAgent {
    
    String handleRequest(
        @UserMessage String request,
        @MemoryId String sessionId,  // 每个用户一个session
        @ToolSet DatabaseTools tools
    );
}
```

```java
// 第一轮
agent.handleRequest("我叫张三", "session-001", tools);
// AI: "你好张三，有什么可以帮你的？"

// 第二轮
agent.handleRequest("我叫什么名字？", "session-001", tools);
// AI: "你叫张三。"
```

## RAG：让AI读你的文档

AI不了解你的业务。用RAG让AI基于你的文档回答问题。

```java
@Configuration
public class RagConfig {
    
    @Bean
    public EmbeddingStore<TextSegment> embeddingStore() {
        return PgVectorEmbeddingStore.builder()
            .host("localhost")
            .port(5432)
            .database("myapp")
            .user("postgres")
            .password("password")
            .table("embeddings")
            .dimension(1536)
            .build();
    }
    
    @Bean
    public ContentRetriever retriever(
            EmbeddingStore<TextSegment> store,
            EmbeddingModel model) {
        return EmbeddingStoreContentRetriever.builder()
            .embeddingStore(store)
            .embeddingModel(model)
            .maxResults(5)
            .minScore(0.75)
            .build();
    }
}
```

```java
@AiService
public interface DocumentAssistant {
    
    @SystemMessage("根据以下文档内容回答问题。如果文档中没有相关信息，请如实告知。")
    String answer(
        @UserMessage String question,
        @Embedding Match documents  // 自动从向量库检索
    );
}
```

## 成本控制实战

LLM API调用是要钱的。生产环境必须做成本控制。

### 1. 选择合适的模型

| 任务 | 推荐模型 | 成本 |
|------|----------|------|
| 简单问答 | gpt-4o-mini | $0.15/1M tokens |
| 复杂推理 | gpt-4o | $2.5/1M tokens |
| 嵌入 | text-embedding-3-small | $0.02/1M tokens |

**经验：80%的任务用mini模型就够了。**

### 2. 缓存重复查询

```java
@Bean
public ChatLanguageModel cachedModel(ChatLanguageModel delegate) {
    return CachingChatLanguageModel.builder()
        .delegate(delegate)
        .expireAfterWrite(Duration.ofMinutes(30))
        .maximumSize(1000)
        .build();
}
```

### 3. Token计数和限流

```java
@Component
public class TokenBudget {
    
    private final AtomicLong usedTokens = new AtomicLong(0);
    private final long dailyBudget = 1_000_000; // 每天限制100万token
    
    public boolean tryConsume(int tokens) {
        long current = usedTokens.addAndGet(tokens);
        if (current > dailyBudget) {
            usedTokens.addAndGet(-tokens);
            return false;
        }
        return true;
    }
}
```

## 完整项目结构

```
src/main/java/com/example/ai/
├── config/
│   ├── RagConfig.java
│   └── TokenBudget.java
├── agent/
│   ├── CustomerServiceAgent.java
│   └── DocumentAssistant.java
├── tools/
│   ├── DatabaseTools.java
│   └── SearchTools.java
└── controller/
    └── ChatController.java
```

## 下一步

- 接入Claude/Gemini等多模型
- 添加流式响应（SSE）
- 构建自定义Prompt模板
- 实现Agent的链式调用（多步推理）

## 总结

LangChain4j让Java开发者构建AI Agent变得异常简单。核心就是三件事：

1. **@AiService** 定义AI接口
2. **@Tool** 定义工具
3. **@MemoryId** 管理对话上下文

5分钟搭起来，5天能做出产品。**先跑起来再说。**

*有问题欢迎留言讨论。关注公众号「JavaDev AI」获取更多实战教程。*
