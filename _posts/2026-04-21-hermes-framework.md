---
title: "Hermes 框架深度解析（基于源码）"
date: 2026-04-21
categories: [人工智能]
tags: [Hermes, AI Agent, Nous Research, Skills, MCP, Qwen, 办公自动化]
excerpt: "严格对照 https://github.com/NousResearch/hermes-agent 源码，重新梳理 Hermes Agent 的真实定位、目录结构、学习闭环、skills/ 目录下全部 25 个类目的用途，以及日常办公可以直接落地的使用场景。"
---

> 之前的一篇《Hermes 框架深度解析》里出现了不少凭印象写出的模块与 API，和 [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) 源码对不上。这一篇**完全按照仓库真实代码结构重写**：所有目录、文件名、skill 名称都能在源码里一一对应，没有杜撰的"Orchestrator""ReAct + Reflection 双引擎""knowledge_retrieve skill"等内容。

---

## 一、重新认识 Hermes Agent

### 1.1 它到底是什么

Hermes Agent 是 [Nous Research](https://nousresearch.com) 开源的**自我改进型个人 AI 智能体**（self-improving AI agent），而不是像 LangGraph、AutoGen 那样的"企业编排框架"。仓库 README 里给出的一句话定位是：

> *"The self-improving AI agent built by Nous Research. It's the only agent with a built-in learning loop — it creates skills from experience, improves them during use, nudges itself to persist knowledge, searches its own past conversations, and builds a deepening model of who you are across sessions."*

所以它的核心气质是：

- **面向个人 / 开发者**，在终端里直接 `hermes` 就能开聊；
- **内建学习闭环**：会自己从会话经验中**创建新 skill**、在使用中**自我改进**、定期**提醒自己沉淀知识**；
- **跨会话记忆**：基于 SQLite + **FTS5 全文检索**做历史会话回忆，并通过 [Honcho](https://github.com/plastic-labs/honcho) 维护"你是谁"的用户模型；
- **"哪里都能用"**：除了 CLI/TUI，还自带 Telegram、Discord、Slack、WhatsApp、Signal、QQBot、HomeAssistant 等 gateway；
- **模型无关**：Nous Portal、OpenRouter、NVIDIA NIM、z.ai/GLM、Kimi、MiniMax、Hugging Face、OpenAI 或**你自己跑的 Qwen / vLLM / llama.cpp 端点**都能接，`hermes model` 一条命令切换。

### 1.2 它不是什么（避免被同类项目带偏）

| 常见误解 | 实际情况 |
| --- | --- |
| 企业级多 Agent 编排平台 | 定位是个人助手，"多 agent" 通过 `delegate_tool` + subagent 实现 |
| 声明式 YAML 建 Agent（类似 CrewAI） | 没有 "agent 配置文件"。你通过 `~/.hermes/config.yaml` 配**设置/密钥**，skill 以目录的形式直接投进 `~/.hermes/skills/` |
| 内置 Orchestrator / Workflow Engine | 并没有一个叫 "Orchestrator" 的模块。编排靠 `run_agent.py` 里的 `AIAgent.run_conversation()` 同步主循环 + `tools/delegate_tool.py` 派发 |
| 自研 Guardrails | 没有独立的护栏框架；有 `tools/approval.py` 做"危险命令探测"，配合 terminal callbacks 走人工确认 |

---

## 二、代码结构深度解析

下面这棵树直接对照仓库 [`AGENTS.md`](https://github.com/NousResearch/hermes-agent/blob/main/AGENTS.md) + 根目录 listing，每个条目我都能在 GitHub 上点进去：

```
hermes-agent/
├── run_agent.py            # 核心：AIAgent 类，run_conversation() 主循环
├── cli.py                  # HermesCLI：交互式 CLI 的总调度
├── model_tools.py          # 工具编排：discover_builtin_tools() / handle_function_call()
├── toolsets.py             # Toolset 定义、_HERMES_CORE_TOOLS 列表
├── mcp_serve.py            # 把 Hermes 自身暴露为 MCP server
├── batch_runner.py         # 批量轨迹生成（训练数据/评测）
├── rl_cli.py               # RL 训练 CLI（配合 environments/）
├── mini_swe_runner.py      # SWE-bench 风格任务跑分
├── trajectory_compressor.py# 轨迹压缩（训练下一代工具调用模型）
├── hermes_state.py         # SessionDB：SQLite 会话存储 + FTS5 全文检索
├── hermes_logging.py / hermes_time.py / hermes_constants.py / utils.py
│
├── agent/                  # Agent 内部件（prompt 构造、上下文压缩、模型元信息等）
│   ├── prompt_builder.py
│   ├── context_compressor.py
│   ├── prompt_caching.py     # Anthropic prompt caching
│   ├── auxiliary_client.py   # 辅助 LLM：视觉理解 / 摘要
│   ├── model_metadata.py / models_dev.py
│   ├── display.py            # KawaiiSpinner 表情动画、工具输出流
│   ├── skill_commands.py     # skill 斜杠命令（CLI/gateway 共享）
│   └── trajectory.py
│
├── hermes_cli/             # `hermes` 所有子命令的实现
│   ├── main.py / setup.py / callbacks.py
│   ├── skills_config.py      # hermes skills：启/禁 skill
│   ├── skills_hub.py         # /skills 斜杠命令（搜索 / 浏览 / 安装）
│   ├── tools_config.py       # hermes tools
│   ├── model_switch.py       # /model 切换
│   ├── skin_engine.py        # CLI 皮肤/主题
│   └── commands.py           # COMMAND_REGISTRY：斜杠命令单一真相源
│
├── tools/                  # 每个工具一个文件
│   ├── registry.py           # 中央工具注册表
│   ├── approval.py           # 危险命令探测（rm -rf、curl | sh 等）
│   ├── terminal_tool.py      # 终端执行（配合 environments/ 跨后端）
│   ├── file_tools.py         # read/write/search/patch
│   ├── web_tools.py          # Parallel + Firecrawl 的搜索/抓取
│   ├── browser_tool.py       # Browserbase 浏览器自动化
│   ├── code_execution_tool.py# execute_code 沙箱
│   ├── delegate_tool.py      # subagent 派发（多路并行）
│   ├── mcp_tool.py           # MCP 客户端（~1050 行）
│   ├── process_registry.py   # 后台进程管理
│   └── environments/         # terminal 后端：local / docker / ssh / daytona / singularity / modal
│
├── gateway/                # 消息平台网关
│   ├── run.py / session.py
│   └── platforms/            # telegram / discord / slack / whatsapp / signal / qqbot / homeassistant
│
├── ui-tui/                 # Ink (React) 终端 UI —— `hermes --tui`
├── tui_gateway/            # TUI 的 Python JSON-RPC 后端
├── acp_adapter/ + acp_registry/  # ACP：VS Code / Zed / JetBrains 集成
├── cron/                   # 内置定时调度器（jobs.py / scheduler.py）
├── environments/           # Atropos RL 训练环境
├── plugins/                # 插件
│
├── skills/                 # ⭐ 默认启用的 skill 目录（按类目组织）
├── optional-skills/        # 官方维护但默认不启用的 skill（通过 `hermes skills install` 启用）
│
├── hermes                  # shell 入口脚本
├── setup-hermes.sh
├── scripts/ install.sh …   # 一键安装脚本（curl | bash）
├── docker/ / Dockerfile / flake.nix / nix/   # 多种部署方式
├── packaging/              # pypi 打包
├── web/ + website/         # 官网（hermes-agent.nousresearch.com）
└── tests/                  # pytest 套件（~3000 条用例）
```

几个容易被忽略、但很能体现 Hermes **真实独特性**的文件/目录：

- **`tools/environments/`**：6 个 terminal 后端（local / docker / ssh / daytona / singularity / modal），让同一个 agent 可以把 `terminal_tool` 的命令**跑在别处**——这是 "Lives where you do" 的底层实现。
- **`cron/`**：把自然语言描述的任务变成定时任务，直接投递到 Telegram/Slack/邮件。每日汇报、夜间备份、每周审计都是它的用武之地。
- **`trajectory_compressor.py` + `batch_runner.py`**：说明 Hermes 同时是一个**数据/训练产物**——可以批量跑轨迹、压缩、用于训练下一代工具调用模型（配合 Atropos）。
- **`acp_adapter/`**：实现了 ACP（Agent Client Protocol），允许 VS Code / Zed / JetBrains 把 Hermes 当作 coding 后端来用。

---

## 三、核心架构：自我改进的学习闭环

Hermes "和别人不一样"的地方，其实不是它的主循环（那就是个标准的 tool-calling loop），而是**围绕主循环一圈的四个闭环模块**：

<div class="mermaid">
graph LR
    U[用户消息] --> L[AIAgent.run_conversation<br/>主循环]
    L -->|工具调用| T[tools/*]
    T --> L
    L --> R[回答]

    subgraph 闭环["🌀 自我改进闭环"]
        M[SessionDB<br/>FTS5 全文检索]
        S[~/.hermes/skills/<br/>自动创建 & 自我改进]
        H[Honcho<br/>用户建模]
        N[Nudges<br/>周期性自我提醒]
    end

    L -.写入.-> M
    M -.检索历史.-> L
    L -.沉淀.-> S
    S -.加载.-> L
    L -.观察.-> H
    H -.用户画像.-> L
    N -.提醒沉淀知识.-> L
</div>

对应到源码：

| 能力 | 主要实现位置 |
| --- | --- |
| 主循环（OpenAI 格式 messages 循环） | `run_agent.py` 的 `AIAgent.run_conversation()` |
| 工具注册与分发 | `tools/registry.py` + `model_tools.py::handle_function_call()` |
| 跨会话搜索 | `hermes_state.py` 里的 `SessionDB`（SQLite + FTS5） |
| skill 斜杠命令 & 自创建 | `agent/skill_commands.py` + `hermes_cli/skills_hub.py` |
| 上下文压缩 | `agent/context_compressor.py` |
| prompt 组装与缓存 | `agent/prompt_builder.py` + `agent/prompt_caching.py` |
| 危险命令确认 | `tools/approval.py` + `hermes_cli/callbacks.py` |
| 子 agent 并行 | `tools/delegate_tool.py` |

**一个很关键的设计决定**：skill 被注入的时机是**用户消息**，而不是系统提示（见 `agent/skill_commands.py` 注释：*"injects as user message (not system prompt) to preserve prompt caching"*）。这是为了让 Anthropic 的 prompt caching 继续命中，是个很实际的工程 trick。

---

## 四、`skills/` 目录全景：25 个类目逐一解析

仓库 `skills/` 下按**类目（category）**组织，每个类目下放着若干**具体 skill**。每个 category 通常配一个 `DESCRIPTION.md`（向 agent 描述该类目做什么），每个具体 skill 目录里放 `SKILL.md`（agentskills.io 开放标准）。

> 下面 25 个类目的用途描述，都直接引自各自的 `DESCRIPTION.md` / `SKILL.md`，没有我自己的再发挥。

### 4.1 `apple/` —— Apple/macOS 自动化

> *"Apple/macOS-specific skills — iMessage, Reminders, Notes, FindMy, and macOS automation. These skills only load on macOS systems."*

包含的 skill：

- **apple-notes**：读写 Apple Notes（备忘录）。
- **apple-reminders**：操作提醒事项（创建/查询/完成）。
- **findmy**：读取 FindMy 的位置数据（找设备 / 家人位置）。
- **imessage**：收发 iMessage。

### 4.2 `autonomous-ai-agents/` —— 派发其他编码 Agent

> *"Skills for spawning and orchestrating autonomous AI coding agents and multi-agent workflows — running independent agent processes, delegating tasks, and coordinating parallel workstreams."*

- **claude-code**：调用 Anthropic 的 Claude Code CLI。
- **codex**：调用 OpenAI Codex CLI。
- **hermes-agent**：在 Hermes 里**再起一个 Hermes 子进程**（用于长任务隔离）。
- **opencode**：调用 OpenCode 这一开源 coding agent。

这是"用 agent 管 agent"的典型实践：主 agent 负责规划，子 agent 负责独立跑长任务，结果回收。

### 4.3 `creative/` —— 创意内容生成

> *"Creative content generation — ASCII art, hand-drawn style diagrams, and visual design tools."*

- **architecture-diagram**：画架构图。
- **ascii-art** / **ascii-video**：生成 ASCII 艺术画/视频。
- **baoyu-comic** / **baoyu-infographic**：宝玉风格的漫画/信息图。
- **creative-ideation**：结构化的"头脑风暴"流程。
- **excalidraw**：生成手绘风格的 Excalidraw 图。
- **manim-video**：用 Manim 做数学/解释动画。
- **p5js**：生成 p5.js 可交互视觉。
- **pixel-art**：像素画生成。
- **popular-web-designs**：仿流行 Web 设计风格。
- **songwriting-and-ai-music**：歌词创作 + AI 音乐。

### 4.4 `data-science/` —— 交互式数据分析

> *"Skills for data science workflows — interactive exploration, Jupyter notebooks, data analysis, and visualization."*

- **jupyter-live-kernel**：让 agent 持有一个**常驻的 Jupyter 内核**，变量、DataFrame 跨 cell 存活。这和 `execute_code` 那种一次性沙箱是两种截然不同的数据分析模式——前者适合 EDA。

### 4.5 `devops/` —— DevOps 事件

- **webhook-subscriptions**：订阅 webhook 事件，把外部系统（GitHub、Stripe、Vercel…）的事件引入到 agent 的上下文中。

### 4.6 `diagramming/` —— 通用绘图类目

> *"Diagram creation skills for generating visual diagrams, flowcharts, architecture diagrams, and illustrations using tools like Excalidraw."*

目前只有 `DESCRIPTION.md` 占位，作为**类目锚点**：给 agent 一个"遇到画图需求应该去哪找 skill"的提示，具体实现复用 `creative/` 里的 excalidraw/architecture-diagram。

### 4.7 `dogfood/` —— 浏览器 QA 自测

这个类目没有 `DESCRIPTION.md`，而是直接一个 `SKILL.md`（它本身就是一个完整 skill）。摘录自源码：

> *"Systematic exploratory QA testing of web applications — find bugs, capture evidence, and generate structured reports."*

五阶段工作流（Plan → Explore → Collect Evidence → Categorize → Report），用 `browser_*` 系列工具真实操作浏览器：导航、快照、点击、填表、截图、读 console 错误，最后用 `templates/dogfood-report-template.md` 模板输出结构化 bug 报告。非常适合**产品上线前的自测回归**。

### 4.8 `domain/` —— 被动域名情报

源码里 `DESCRIPTION.md` 写得非常具体（引用原文）：

> *"Passive domain reconnaissance using Python stdlib. Use this skill for subdomain discovery, SSL certificate inspection, WHOIS lookups, DNS records, domain availability checks, and bulk multi-domain analysis. No API keys required."*

能力清单：

- 通过 **crt.sh 证书透明日志**挖子域；
- 在线探测 SSL/TLS 证书（有效期、cipher、SAN、TLS 版本）；
- 直连 100+ TLD 的 **WHOIS** 服务器；
- 查 A/AAAA/MX/NS/TXT/CNAME；
- 判断域名是否可注册；
- 支持 20 个域名并行**批量分析**。

**零依赖、零 API key**，纯 Python stdlib，是个很"工匠"的小工具。

### 4.9 `email/` —— 终端邮件

> *"Skills for sending, receiving, searching, and managing email from the terminal."*

- **himalaya**：调用 [Himalaya CLI](https://github.com/pimalaya/himalaya) 处理邮件（IMAP/JMAP/Maildir/Notmuch）。

### 4.10 `feeds/` —— 内容订阅

> *"Skills for monitoring, aggregating, and processing RSS feeds, blogs, and web content sources."*

### 4.11 `gaming/` —— 游戏相关

- **minecraft-modpack-server**：Minecraft 服务端 + 整合包管理。
- **pokemon-player**：让 agent "玩宝可梦"（偏研究/演示性质）。

### 4.12 `gifs/` —— GIF 动图

> *"Skills for searching, downloading, and working with GIFs and short-form animated media."*

### 4.13 `github/` —— GitHub 工作流

> *"GitHub workflow skills for managing repositories, pull requests, code reviews, issues, and CI/CD pipelines using the gh CLI and git via terminal."*

- **codebase-inspection**：对仓库代码做结构化检视。
- **github-auth**：处理 gh / token 鉴权。
- **github-code-review**：PR code review 流程。
- **github-issues**：issue 增删查改。
- **github-pr-workflow**：开 PR / 合并 / rebase 的标准流程。
- **github-repo-management**：仓库级别的管理（settings、labels、分支保护等）。

### 4.14 `index-cache/` —— 外部 skill 索引缓存

这是给 `skills_hub` 用的**元数据 skill**，里面是几个 JSON 文件：

- `anthropics_skills_skills_.json` —— 缓存 Anthropic 官方 skills 仓索引；
- `claude_marketplace_anthropics_skills.json` —— Claude 市场索引；
- `lobehub_index.json` —— LobeHub 索引；
- `openai_skills_skills_.json` —— OpenAI 侧索引。

配合 `hermes skills browse / search / install` 使用，用户能从**多个生态**一键装第三方 skill。

### 4.15 `inference-sh/` —— 统一云端 AI 应用

> *"Run 150+ AI applications in the cloud via the inference.sh platform. One API key for everything — access image generation, video creation, LLMs, search, 3D, and more through a single account."*

- **cli**：通过 `infsh` CLI 在终端里调用云端的 FLUX、Veo、Wan、Seedance、Claude、Gemini、Tavily、Exa、Rodin、TTS 等。一个 key 搞定全部外部模型。

### 4.16 `mcp/` —— 原生 MCP 客户端

> *"Skills for working with MCP (Model Context Protocol) servers, tools, and integrations. Documents the built-in native MCP client — configure servers in config.yaml for automatic tool discovery."*

- **native-mcp**：告诉 agent 如何通过 `~/.hermes/config.yaml` 声明 MCP server，Hermes 自动发现并挂载这些 server 上的 tool。

### 4.17 `media/` —— 多媒体

> *"Skills for working with media content — YouTube transcripts, GIF search, music generation, and audio visualization."*

- **gif-search**：GIF 搜索（与 `gifs/` 呼应）。
- **heartmula**：音频可视化。
- **songsee**：听歌识别/音乐相关。
- **youtube-content**：拉 YouTube 字幕/转录做分析。

### 4.18 `mlops/` —— 模型工程

> *"Knowledge and Tools for Machine Learning Operations — tools and frameworks for training, fine-tuning, deploying, and optimizing ML/AI models."*

- **evaluation**：模型评测。
- **huggingface-hub**：HF hub 操作（下载/上传权重、数据集）。
- **inference**：推理部署。
- **models**：模型文件管理。
- **research**：ML 研究流程。
- **training**：训练流程。
- **vector-databases**：向量库（和企业常说的 "RAG 后端"是同一件事）。

### 4.19 `note-taking/` —— 笔记

> *"Note taking skills, to save information, assist with research, and collab on multi-session planning and information sharing."*

- **obsidian**：读写 Obsidian vault（md 文件、frontmatter、反链）。

### 4.20 `productivity/` —— 日常办公主力

> *"Skills for document creation, presentations, spreadsheets, and other productivity workflows."*

- **google-workspace**：Gmail / Drive / Docs / Sheets / Calendar。
- **linear**：Linear（研发项目管理）。
- **maps**：地图/路线。
- **nano-pdf**：PDF 读写、拆分、合并、提取。
- **notion**：Notion 页面/数据库。
- **ocr-and-documents**：扫描件/图片 OCR、文档解析。
- **powerpoint**：生成/修改 PowerPoint 文件。

这一组就是**"真·办公自动化"的主力军**，后文第 6 章专门展开办公场景会回到它们。

### 4.21 `red-teaming/` —— 安全 / 红队

- **godmode**：LLM 红队攻击/越狱研究（Nous 是研究机构，保留这类能力是为了**做安全研究**，不是鼓励滥用）。

### 4.22 `research/` —— 学术与情报

> *"Skills for academic research, paper discovery, literature review, domain reconnaissance, market data, content monitoring, and scientific knowledge retrieval."*

- **arxiv**：arXiv 论文检索/下载。
- **blogwatcher**：技术博客监控。
- **llm-wiki**：LLM 领域知识百科。
- **polymarket**：Polymarket 预测市场数据。
- **research-paper-writing**：按学术规范写论文。

### 4.23 `smart-home/` —— 智能家居

> *"Skills for controlling smart home devices — lights, switches, sensors, and home automation systems."*

- **openhue**：控制飞利浦 Hue 灯。

### 4.24 `social-media/` —— 社交平台

> *"Skills for interacting with social platforms and social-media workflows — posting, reading, monitoring, and account operations."*

- **xurl**：用 [xurl](https://github.com/xdevplatform/xurl) 操作 Twitter/X API。

### 4.25 `software-development/` —— 软件开发方法论

这个类目下**没有 DESCRIPTION.md**，全是方法论 skill：

- **plan**：任何复杂任务前先出计划。
- **writing-plans**：写高质量设计文档/方案。
- **requesting-code-review**：怎么正确请别人 review 你的 PR。
- **subagent-driven-development**：用子 agent 并行拆分开发任务。
- **systematic-debugging**：系统化 debug（假设—隔离—验证）。
- **test-driven-development**：TDD 流程。

这组 skill 很有意思：它们不是"能调用 XX API"，而是**行为准则**——被注入到用户消息后，让 agent 把"coding 过程"做得更像一位资深工程师。

### 4.26 `optional-skills/`（默认不启用，但同为官方）

根据仓库 `optional-skills/DESCRIPTION.md`：

> *"Official skills maintained by Nous Research that are **not activated by default**. They ship with the hermes-agent repository but are not copied to `~/.hermes/skills/` during setup. Discoverable via `hermes skills browse` and installable via `hermes skills install <identifier>`."*

目录下的额外类目有：**blockchain、communication、health、migration、security、web-development**，以及与 `skills/` 重名的扩展版 **autonomous-ai-agents / creative / devops / dogfood / email / mcp / mlops / productivity / research**（里面是"更重"或更小众的 skill）。默认不装是因为它们依赖外部 API key 或体积/依赖较大。

---

## 五、平时办公可以用 Hermes 的场景

既然 skill 才是 Hermes 真正的"能力单元"，**办公场景 = skill 的组合调用**。下面这一组都是我对着上面那 25 个类目挑出来、**确实能在仓库里找到对应 skill** 的真实用法。

### 5.1 邮件 + 日程 + 文档：每日上班第一小时

涉及 skill：`email/himalaya`、`productivity/google-workspace`、`productivity/notion`、`note-taking/obsidian`

能做的事：

- "把我昨晚到今早的未读邮件按**发件人**聚合成摘要，重要的 3 封给我完整内容，其余一句话。" → `himalaya` 读，auxiliary LLM 摘要。
- "把今天 Google Calendar 的会议按**是否我要讲**分成两列。" → `google-workspace`。
- "昨晚那封季度汇报邮件里的要点 → 建一个 Notion 页面 + 同步一份到 Obsidian vault。" → `notion` + `obsidian` 并行写。

### 5.2 PPT / 报表 / PDF 批处理

涉及 skill：`productivity/powerpoint`、`productivity/nano-pdf`、`productivity/ocr-and-documents`、`data-science/jupyter-live-kernel`

- "从 `2025 年业务复盘.pptx` 抽出所有 chart 数据 → 合成一张周度趋势图 → 作为新 slide 插回第 12 页后面。" → `powerpoint` + `jupyter-live-kernel`。
- "客户发来的 30 份 PDF 合同，**OCR** 后把签署日期、金额、甲乙方提取到一张表。" → `ocr-and-documents` + `nano-pdf`。
- "这份 120 页 PDF 太大，**按章节拆**成 7 个 PDF。" → `nano-pdf`。

### 5.3 研发/ OSS 工作流

涉及 skill：`github/*`、`software-development/*`、`autonomous-ai-agents/*`

- "过去 7 天所有 `needs-triage` issue → 按优先级排序，给前 5 条写回复草稿。" → `github-issues`。
- "这个 bug 我已经看了 3 小时没头绪。" → 启动 `systematic-debugging` skill，让 agent 按"复现 → 二分 → 假设 → 验证"一步步来。
- "把这个功能拆成 3 个独立子任务，分别派 `claude-code` / `codex` / `opencode` 各跑一版，最后对比。" → `autonomous-ai-agents` + `delegate_tool`。
- "帮我 review 这个 PR，并按 `requesting-code-review` 的方式把反馈写给同事。" → `github-code-review` + `requesting-code-review`。

### 5.4 调研、情报、竞对监控

涉及 skill：`research/arxiv`、`research/blogwatcher`、`feeds/*`、`domain/*`、`social-media/xurl`、`media/youtube-content`

- "最近 30 天 ArXiv 上关于 `MoE routing` 的高引论文 TL;DR。" → `arxiv`。
- "每天 9 点把这 12 个技术博客的新文章拉一遍，标题+2 句摘要发我 Telegram。" → `feeds` + `blogwatcher` + `cron/` + `gateway/platforms/telegram`。
- "竞品 `example.com` 的所有子域、SSL 快到期的有哪些？" → `domain`（零外部 key）。
- "这个发布会 2 小时的 YouTube 视频，给我 10 条关键结论。" → `media/youtube-content`。

### 5.5 笔记沉淀 + 跨会话记忆

涉及组件：`hermes_state.py`（FTS5 会话检索）、`note-taking/obsidian`、周期性 nudge

- "上次我们讨论那个 xxx 方案是什么时候？" → Hermes 直接 FTS5 搜自己历史会话。
- 只要开了 nudge，Hermes 会**主动提醒你**："这次讨论里有几个结论我打算沉淀到你的 Obsidian 的 `decisions/2026-04.md`，确认一下？"

### 5.6 定时自动化（不用写任何 cron 语法）

涉及：`cron/` + 任意 skill + 任意 `gateway/platforms/*`

- 每天 18:00 汇总今天的 GitHub 活动 + 邮件要点 + Calendar 明天安排 → Telegram。
- 每周一 9:00 把上周 Linear 任务的完成率生成一张图 → 发 Slack 频道。
- 每晚 2:00 对 `~/work` 目录差异备份到 S3，并发邮件告知结果。

全部用**自然语言描述**，不用写 shell 脚本、不用记 crontab 写法。

### 5.7 远程操控：在手机上让 PC 跑活

Hermes 的 gateway 支持 Telegram/WhatsApp/Signal 等，结合 `tools/environments/` 里的 **ssh / docker / daytona / modal** 后端：

- 出门在外，Telegram 发一句"**跑一下昨晚那个数据分析脚本，产出结果发我**"——Hermes 跑在你家里的机器，日志流式回给你手机；
- 或者让它跑在 Daytona/Modal 的 serverless 环境上，**闲时休眠，几乎零花费**，一条消息唤醒。

### 5.8 "把我自己写进去"：个人模型沉淀

Honcho 负责从对话里学习你的偏好（写作风格、常用工具、决策风格）。用得越久，越知道：

- 你 PPT 喜欢先封面后目录还是开门见山；
- 你回邮件习惯中英混排还是全中文；
- 你跟下属讨论倾向于先肯定再提改进，还是直接给建议。

这是 Hermes 区别于一般"每次都从零开始"助手的关键——**它会随你成长**。

---

## 六、本地 / 私有模型（含 Qwen）如何接入

Hermes 不锁定模型。任何 **OpenAI 兼容端点**都能用，本地 Qwen 是最经典的路径：

1. 用 vLLM / llama.cpp / Ollama / SGLang 起一个 OpenAI 兼容 server（`/v1/chat/completions`）。
2. 执行 `hermes model`，选 "Custom OpenAI-compatible"，填上 base URL（如 `http://127.0.0.1:8000/v1`）和模型名（如 `Qwen2.5-32B-Instruct`）。
3. 后续所有 skill、tool、gateway 都跟着新模型走，**不用改任何 skill 代码**。

需要注意：

- Qwen 系列建议选带 **function call / tools 支持**的 Instruct 版本；否则 `tools/` 里的 tool schema 派发会失效。
- 上下文窗口较小时，`agent/context_compressor.py` 会自动压缩历史；仍建议把常用 skill 通过 `hermes skills` **只启用必要的几个**，避免每次塞太多说明进 prompt。
- 云端 + 本地混用：可以把**规划**这一步用 Claude/Qwen-Max，**执行代码/读文件**用本地小模型，靠 `tools/delegate_tool.py` 拆开——成本 & 隐私兼顾。

---

## 七、总结

这次"严格对源码"重写之后，对 Hermes 的认识应当更接近它的真面目：

- 它是 Nous Research 做的**个人向自我改进 agent**，而不是 LangGraph/CrewAI 那样的企业编排框架；
- 核心价值是 **"学习闭环 + 丰富 skill 生态 + 多端存在"**，代码层面一切围绕 `AIAgent.run_conversation` 这一个同步主循环展开；
- `skills/` 下的 25 个类目基本覆盖了个人知识工作者的日常：Apple、GitHub、邮件、Office、Notion/Obsidian、OCR/PDF、ArXiv、社交、智能家居、MLOps、安全研究；`optional-skills/` 另外还有 blockchain/health/web-development 等；
- 办公使用不必"配编排"，**把需求说清楚 + 让合适的 skill 自动被调用**就是正确姿势；必要时用 `cron/` 自动化、用 `gateway/*` 跨端。

如果之前那篇文章让你 clone 下来对不上号，以这一篇为准 🙏。

---

> 📚 **核对链接（每一节都有对应源码路径）**
>
> - 仓库：<https://github.com/NousResearch/hermes-agent>
> - 开发者指南：<https://github.com/NousResearch/hermes-agent/blob/main/AGENTS.md>
> - 官方文档：<https://hermes-agent.nousresearch.com/docs/>
> - Skills 目录：<https://github.com/NousResearch/hermes-agent/tree/main/skills>
> - Optional Skills：<https://github.com/NousResearch/hermes-agent/tree/main/optional-skills>
