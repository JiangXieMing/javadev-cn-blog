---
title: "AI 编程工具配置教程：Claude Code、zcode、Copilot 等 7 款工具一键接入 GLM 模型"
date: 2026-06-30
draft: false
tags: ["AI编程", "Claude Code", "zcode", "Cursor", "教程", "GLM"]
description: "手把手教你将 Claude Code、zcode、GitHub Copilot、Crush(opencode)、CodeGPT、Cursor、Trae 共 7 款 AI 编程工具接入中转站 API，统一使用 GLM 系列模型。一个 API Key，所有工具通用。"
---

> **🔥 [API Key 购买地址](https://tinyapi.nykjsd.cn/home)** — 本教程所有工具统一使用同一个 API Key，无需单独注册各平台账号。价格实惠，一键购买，支持 GLM、Claude、GPT 等主流模型。

## 通用信息

- **API Base URL**：`https://tinyapi.nykjsd.cn`
- **模型推荐**：`glm-5.2`（主力模型，效果最佳）、`glm-5`（轻量快速，适合简单任务）
- **API Key**：[👉 点击购买](https://tinyapi.nykjsd.cn/home)，格式为 `sk-xxx…xxxx`

---

## 一、Claude Code（⭐ 推荐最强 AI 编程助手）

Claude Code 是 Anthropic 推出的 AI 编码助手，能理解整个代码库，跨多个文件编辑代码、运行命令。支持终端 CLI、VS Code 插件、JetBrains 插件、桌面应用和浏览器。

### 1. 安装

官方文档：https://code.claude.com/docs/zh-CN/overview

> ⚠️ 安装需要梯子，站点选择美国地区。

**推荐方式（需 Node.js 18+）：**

```bash
npm install -g @anthropic-ai/claude-code
```

**macOS / Linux / WSL：**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell：**

```powershell
irm https://claude.ai/install.ps1 | iex
```

**Windows CMD：**

```batch
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

**Homebrew（macOS）：**

```bash
brew install --cask claude-code
```

安装完成后根据提示登录或配置环境变量，重启终端，输入 `claude` 回车，出现 Claude 对话界面即安装成功。

### 2. 配置模型（接入中转站 API）

打开/创建配置文件：

- macOS / Linux：`~/.claude/settings.json`
- Windows：`C:\Users\{你的用户名}\.claude\settings.json`

> ⚠️ 注意：使用你的实际用户名路径，不要用 Administrator。

填入以下内容（将 `your_zhipu_api_key` 替换为你的 API Key）：

```json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "your_zhipu_api_key",
    "ANTHROPIC_BASE_URL": "https://tinyapi.nykjsd.cn",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "glm-5",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "glm-5.2",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "glm-5.2",
    "API_TIMEOUT_MS": "3000000",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": 1
  },
  "model": "opus"
}
```

同时在 `~/.claude.json`（Windows 为 `用户目录/.claude.json`）中添加：

```json
{
  "hasCompletedOnboarding": true
}
```

### 3. 验证

重启终端，进入项目目录，输入 `claude` 回车，正常对话即配置成功。

若遇到「Do you want to use this API key」选择 Yes 即可。

### 4. VS Code / JetBrains 插件

- **VS Code**：安装 Claude Code 插件（扩展市场搜索 "Claude Code"），配置方式同上（修改同一份 settings.json）。
- **JetBrains IDE**：从 JetBrains Marketplace 安装 Claude Code 插件，同样读取 settings.json 配置。

测试：打开 VS Code / IDEA，右上角出现 Claude 图标，点击弹出对话框进行对话。

### 5. 配置管理工具：CCSwitch（可选）

CCSwitch 是 Claude Code 的配置管理工具，支持通过可视化界面管理多套供应商配置，方便快速切换不同模型和 API 源。

> 📌 不是必须安装，手动编辑 settings.json 也能完成配置。

配置步骤：
1. 打开 CCSwitch，选择 Anthropic 供应商创建配置
2. 在配置 JSON 窗口中粘贴上方配置并保存

---

## 二、zcode（智谱 AI 编程助手）

zcode 是智谱 AI 官方推出的 AI 编程助手，原生使用 GLM 系列模型。支持终端和 IDE 插件，可通过中转站 API 接入。

### 1. 安装

**方式一：一键安装助手（推荐）**

```bash
npx @z_ai/coding-helper
```

按照界面提示操作即可自动完成工具安装和套餐配置。

**方式二：VS Code 插件**

在 VS Code 插件市场搜索 zcode 并安装。

### 2. 配置模型（接入中转站 API）

zcode 原生使用 GLM 模型，但通过中转站接入时需配置自定义 API 端点。

打开/创建配置文件：

- macOS / Linux：`~/.zcode/settings.json`
- Windows：`C:\Users\{你的用户名}\.zcode\settings.json`

填入以下内容：

```json
{
  "api_key": "***",
  "base_url": "https://tinyapi.nykjsd.cn/v1",
  "model": "glm-5.2",
  "timeout": 300
}
```

> ⚠️ 具体配置文件路径和格式以 zcode 实际版本为准，建议优先使用一键安装助手 `npx @z_ai/coding-helper` 自动配置。

### 3. 使用建议

- 首选模型：`glm-5.2`（效果最佳）
- 轻量任务：`glm-5`（响应更快）
- zcode 为智谱官方工具，GLM 模型兼容性最好

---

## 三、GitHub Copilot

GitHub Copilot 是 GitHub 推出的 AI 编程助手，深度集成 VS Code、Visual Studio、JetBrains 等 IDE。

### 1. 安装

- **VS Code**：扩展市场搜索 "GitHub Copilot" 并安装
- **JetBrains IDE**：从 Settings → Plugins 搜索 "GitHub Copilot" 安装
- 需要 GitHub Copilot 订阅（个人 $10/月，或 Copilot Free 免费版）

### 2. 接入中转站 API

> ⚠️ GitHub Copilot 对自定义端点支持有限，以下为参考配置，实际可用性取决于 Copilot 版本。

在 VS Code 设置中添加（settings.json）：

```json
{
  "github.copilot.openai.endpoint": "https://tinyapi.nykjsd.cn/v1",
  "github.copilot.openai.key": "你的中转站API密钥",
  "github.copilot.model": "glm-5.2"
}
```

> 💡 如果 Copilot 自定义端点不可用，建议使用 Claude Code 或 Crush（opencode）替代。

### 3. 验证

打开 VS Code，右键编辑器区域或使用快捷键触发 Copilot 补全，确认模型响应正常。

---

## 四、opencode / Crush（终端 AI 编程助手）

opencode 是一个基于终端的 AI 编程助手（Go 语言编写），现已更名为 **Crush**，由 Charm 团队继续维护。提供 TUI 交互界面，支持多种 AI 模型和自定义 API 端点。

### 1. 安装

```bash
# Homebrew
brew install charmbracelet/tap/crush

# NPM
npm install -g @charmland/crush

# Go
go install github.com/charmbracelet/crush@latest

# Windows (WinGet)
winget install charmbracelet.crush

# Windows (Scoop)
scoop bucket add charm https://github.com/charmbracelet/scoop-bucket.git
scoop install crush
```

### 2. 接入中转站 API

创建配置文件 `~/.config/crush/crush.json`（或项目目录下 `.crush.json`）：

```json
{
  "providers": {
    "openai": {
      "id": "openai",
      "name": "GLM Proxy",
      "base_url": "https://tinyapi.nykjsd.cn/v1",
      "type": "openai",
      "api_key": "***",
      "models": [
        {
          "id": "glm-5.2",
          "name": "GLM 5.2"
        },
        {
          "id": "glm-5",
          "name": "GLM 5"
        }
      ]
    }
  },
  "agents": {
    "coder": {
      "model": "glm-5.2"
    },
    "task": {
      "model": "glm-5.2"
    }
  }
}
```

或通过环境变量配置：

```bash
export OPENAI_API_KEY="***"
export OPENAI_BASE_URL="https://tinyapi.nykjsd.cn/v1"
```

> 💡 Crush 支持会话中途切换模型，非常适合多模型对比使用。

### 3. 使用

在项目目录运行：

```bash
crush
```

首次启动会引导选择模型和配置。

---

## 五、CodeGPT（VS Code 插件）

CodeGPT 是一个 VS Code 插件形式的 AI 编程助手，支持多种模型和自定义 API 端点。

### 1. 安装插件

在 VS Code 插件市场搜索 CodeGPT，点击安装。

### 2. 配置密钥

打开文件夹目录：

```
%USERPROFILE%\.codex
```

找到 `auth.json` 文件，写入：

```json
{
  "OPENAI_API_KEY": "你的中转站API密钥"
}
```

### 3. 配置 URL 和模型

在同一目录下找到 `config.toml` 文件，配置如下：

```toml
model = "glm-5.2"
model_provider = "anyrouter"
preferred_auth_method = "apikey"
personality = "pragmatic"
model_reasoning_effort = "high"

[model_providers.anyrouter]
name = "Any Router"
base_url = "https://tinyapi.nykjsd.cn/v1"
wire_api = "responses"
```

### 4. 验证

完成配置后重启 VS Code，打开 CodeGPT 进行测试对话。

---

## 六、Cursor

Cursor 是基于 VS Code 深度定制的 AI 代码编辑器，内置 AI 编程功能，支持自定义模型配置。

> ⚠️ 必须使用 Cursor Pro 或更高版本订阅，免费版不支持自定义 Base URL。

### 1. 安装

官网下载：https://cursor.com

下载对应平台安装包，安装过程与 VS Code 类似。

### 2. 接入中转站 API

打开 Cursor 设置：`File → Preferences → Open Cursor Settings`

找到 Models 配置区域，点击 Override Model 或 Create New Model，按以下信息填写：

| 配置项 | 值 |
|--------|-----|
| Model Name | glm-5.2 |
| Base URL | https://tinyapi.nykjsd.cn/v1 |
| API Key | 你的中转站API密钥 |
| Model ID | glm-5.2 |

也可以在设置 JSON 中直接配置：

```json
{
  "cursor.customModels": [
    {
      "name": "GLM 5.2",
      "baseURL": "https://tinyapi.nykjsd.cn/v1",
      "apiKey": "***",
      "model": "glm-5.2"
    }
  ]
}
```

### 3. 验证

打开 Cursor，`Ctrl+K` / `Cmd+K` 打开 AI 对话，选择 GLM 5.2 模型进行测试。

---

## 七、Trae（Pro 版本）

Trae 是字节跳动推出的 AI IDE，基于 VS Code 深度定制，Pro 版本支持自定义模型配置。

> ⚠️ 需要支持添加自定义模型的 Pro 版本。

### 1. 安装

官网下载：https://www.trae.ai

下载对应平台安装包并安装。

### 2. 接入中转站 API

Windows 用户修改配置文件：

```
%APPDATA%\Trae\config.yaml
```

在文件中增加以下配置：

```yaml
model_settings:
  retries: 10        # 重试次数
  timeout: 300       # 超时时间（秒）

custom_models:
  - name: "GLM 5.2"
    provider: "openai"
    base_url: "https://tinyapi.nykjsd.cn/v1"
    api_key: "***"
    model: "glm-5.2"
    max_tokens: 32768
```

### 3. 验证

重启 Trae，打开 AI 对话面板，选择 GLM 5.2 模型进行测试。

---

## 常见问题

**Q: 接口超时怎么办？**
A: 建议将超时时间设置为 300 秒以上。Claude Code 可通过 `API_TIMEOUT_MS` 配置，其他工具在对应设置中调整 timeout 参数。

**Q: Claude Code 安装失败？**
A: 确保使用梯子，并将站点切换到美国地区。安装命令需要在 PowerShell 或 CMD 中以管理员身份运行。也可以用 `npm install -g @anthropic-ai/claude-code` 方式安装。

**Q: 模型名称怎么选？**
A: `glm-5.2` — 推荐主力模型，效果最佳；`glm-5` — 轻量快速，适合简单任务和上下文窗口管理。

**Q: API Key 在哪里获取？**
A: 👉 [购买地址](https://tinyapi.nykjsd.cn/home) — 价格实惠，支持 GLM、Claude、GPT 等主流模型。

**Q: 哪个工具最推荐？**
A: Claude Code（⭐ 推荐）功能最强，支持终端、IDE 插件、桌面应用，配置灵活。zcode 是智谱官方工具，GLM 原生兼容性最好。Crush（opencode）适合喜欢终端操作的开发者。

**Q: 中转站的接口协议是什么？**
A: 中转站同时支持 OpenAI 兼容协议和 Anthropic 兼容协议。Claude Code / zcode 使用 Anthropic 协议，其他工具（Cursor / CodeGPT / Crush）使用 OpenAI 协议。

---

> 🔥 **API Key 购买：[https://tinyapi.nykjsd.cn/home](https://tinyapi.nykjsd.cn/home)** — 一个 Key，所有工具通用！
