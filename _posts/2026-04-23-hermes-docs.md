---
title: "Hermes Docs（官方文档简体中文版）"
date: 2026-04-23
layout: hermes-docs
categories: [人工智能]
tags: [Hermes, Nous Research, AI Agent, 中文文档, MCP, Skills, 翻译]
excerpt: "Nous Research 出品的 Hermes Agent 官方文档（https://hermes-agent.nousresearch.com/docs/）简体中文版。左侧为完整目录树，右侧为对应页面内容；支持目录点击切换、文档内跨页跳转、URL hash 深链接。"
permalink: /hermes-docs/
---

<!-- =========================================================================
  Hermes Agent 中文文档 — 数据与内容
  约定：
    * 下面 <script id="hd-nav-data"> 里是侧边栏目录（与官方 sidebars.ts 完全对应）。
    * 每个页面写成 <section data-page="<id>"> … </section>，id 与目录里的 id 完全一致。
    * 跨页内链使用 <a data-page="<id>">…</a>，JS 会拦截并更新 URL hash。
    * 专有名词（Hermes、MCP、Nous Research、Telegram、Discord、Slack、WhatsApp、
      Signal、Matrix、ripgrep、ffmpeg、uv、Ollama、vLLM、SOUL.md 等）保留英文原名。
    * 命令、配置、代码、文件路径、参数名一律不翻译，保持可直接复制运行。
  ========================================================================= -->

<script type="application/json" id="hd-nav-data">
[
  {"type": "category", "label": "快速开始", "items": [
    {"id": "getting-started/quickstart",   "title": "快速上手（Quickstart）"},
    {"id": "getting-started/installation", "title": "安装"},
    {"id": "getting-started/termux",       "title": "Termux / Android"},
    {"id": "getting-started/nix-setup",    "title": "Nix & NixOS 安装"},
    {"id": "getting-started/updating",     "title": "升级 Hermes"},
    {"id": "getting-started/learning-path","title": "学习路径"}
  ]},
  {"type": "category", "label": "使用 Hermes", "items": [
    {"id": "user-guide/cli",                    "title": "CLI 命令行界面"},
    {"id": "user-guide/tui",                    "title": "TUI 终端界面"},
    {"id": "user-guide/configuration",          "title": "配置"},
    {"id": "user-guide/sessions",               "title": "会话管理"},
    {"id": "user-guide/profiles",               "title": "多实例 Profile"},
    {"id": "user-guide/git-worktrees",          "title": "Git Worktree 并行"},
    {"id": "user-guide/docker",                 "title": "在 Docker 中运行"},
    {"id": "user-guide/security",               "title": "安全模型"},
    {"id": "user-guide/checkpoints-and-rollback","title": "检查点与回滚"}
  ]},
  {"type": "category", "label": "功能特性", "items": [
    {"id": "user-guide/features/overview",       "title": "功能总览"},
    {"id": "user-guide/features/tool-gateway",   "title": "Nous Tool Gateway"},
    {"type": "category", "label": "核心", "items": [
      {"id": "user-guide/features/tools",              "title": "工具与工具集"},
      {"id": "user-guide/features/skills",             "title": "Skills 技能系统"},
      {"id": "user-guide/features/memory",             "title": "持久化记忆"},
      {"id": "user-guide/features/memory-providers",   "title": "记忆后端（Memory Providers）"},
      {"id": "user-guide/features/context-files",      "title": "上下文文件"},
      {"id": "user-guide/features/context-references", "title": "上下文引用（@ 引用）"},
      {"id": "user-guide/features/personality",        "title": "Personality 与 SOUL.md"},
      {"id": "user-guide/features/skins",              "title": "皮肤与主题"},
      {"id": "user-guide/features/plugins",            "title": "插件系统"},
      {"id": "user-guide/features/built-in-plugins",   "title": "内置插件"}
    ]},
    {"type": "category", "label": "自动化", "items": [
      {"id": "user-guide/features/cron",             "title": "Cron 定时任务"},
      {"id": "user-guide/features/delegation",       "title": "子代理委派（Delegation）"},
      {"id": "user-guide/features/code-execution",   "title": "代码执行 execute_code"},
      {"id": "user-guide/features/hooks",            "title": "事件 Hook"},
      {"id": "user-guide/features/batch-processing", "title": "批处理（Batch）"}
    ]},
    {"type": "category", "label": "媒体与网页", "items": [
      {"id": "user-guide/features/voice-mode",       "title": "语音模式 Voice Mode"},
      {"id": "user-guide/features/browser",          "title": "浏览器自动化"},
      {"id": "user-guide/features/vision",           "title": "视觉（图像输入）"},
      {"id": "user-guide/features/image-generation", "title": "图像生成"},
      {"id": "user-guide/features/tts",              "title": "TTS 语音合成"}
    ]},
    {"type": "category", "label": "管理", "items": [
      {"id": "user-guide/features/web-dashboard",     "title": "Web Dashboard"},
      {"id": "user-guide/features/dashboard-plugins", "title": "Dashboard 插件"}
    ]},
    {"type": "category", "label": "高级", "items": [
      {"id": "user-guide/features/rl-training", "title": "RL 训练数据"}
    ]},
    {"type": "category", "label": "Skills 精选", "items": [
      {"id": "user-guide/skills/godmode",          "title": "Godmode Skill"},
      {"id": "user-guide/skills/google-workspace", "title": "Google Workspace Skill"}
    ]}
  ]},
  {"type": "category", "label": "消息平台", "items": [
    {"id": "user-guide/messaging/index",          "title": "Messaging Gateway 总览"},
    {"id": "user-guide/messaging/telegram",       "title": "Telegram"},
    {"id": "user-guide/messaging/discord",        "title": "Discord"},
    {"id": "user-guide/messaging/slack",          "title": "Slack"},
    {"id": "user-guide/messaging/whatsapp",       "title": "WhatsApp"},
    {"id": "user-guide/messaging/signal",         "title": "Signal"},
    {"id": "user-guide/messaging/email",          "title": "Email"},
    {"id": "user-guide/messaging/sms",            "title": "SMS（Twilio）"},
    {"id": "user-guide/messaging/homeassistant",  "title": "Home Assistant"},
    {"id": "user-guide/messaging/mattermost",     "title": "Mattermost"},
    {"id": "user-guide/messaging/matrix",         "title": "Matrix"},
    {"id": "user-guide/messaging/dingtalk",       "title": "钉钉 DingTalk"},
    {"id": "user-guide/messaging/feishu",         "title": "飞书 / Lark"},
    {"id": "user-guide/messaging/wecom",          "title": "企业微信 WeCom"},
    {"id": "user-guide/messaging/wecom-callback", "title": "企业微信回调"},
    {"id": "user-guide/messaging/weixin",         "title": "微信公众号 Weixin"},
    {"id": "user-guide/messaging/bluebubbles",    "title": "BlueBubbles（iMessage）"},
    {"id": "user-guide/messaging/qqbot",          "title": "QQ 机器人"},
    {"id": "user-guide/messaging/open-webui",     "title": "Open WebUI / API Server"},
    {"id": "user-guide/messaging/webhooks",       "title": "Webhooks"}
  ]},
  {"type": "category", "label": "集成", "items": [
    {"id": "integrations/index",                    "title": "集成总览"},
    {"id": "integrations/providers",                "title": "AI Providers（模型供应商）"},
    {"id": "user-guide/features/mcp",               "title": "MCP（Model Context Protocol）"},
    {"id": "user-guide/features/acp",               "title": "ACP（编辑器集成）"},
    {"id": "user-guide/features/api-server",        "title": "OpenAI 兼容 API Server"},
    {"id": "user-guide/features/honcho",            "title": "Honcho 用户建模"},
    {"id": "user-guide/features/provider-routing",  "title": "Provider 路由"},
    {"id": "user-guide/features/fallback-providers","title": "Provider 容灾（Fallback）"},
    {"id": "user-guide/features/credential-pools",  "title": "凭证池（Credential Pools）"}
  ]},
  {"type": "category", "label": "指南 & 教程", "items": [
    {"id": "guides/tips",                      "title": "使用技巧与最佳实践"},
    {"id": "guides/local-llm-on-mac",          "title": "在 Mac 上跑本地 LLM"},
    {"id": "guides/daily-briefing-bot",        "title": "每日简报机器人"},
    {"id": "guides/team-telegram-assistant",   "title": "团队 Telegram 助手"},
    {"id": "guides/python-library",            "title": "作为 Python 库使用"},
    {"id": "guides/use-mcp-with-hermes",       "title": "用好 MCP + Hermes"},
    {"id": "guides/use-soul-with-hermes",      "title": "用好 SOUL.md"},
    {"id": "guides/use-voice-mode-with-hermes","title": "用好 Voice Mode"},
    {"id": "guides/build-a-hermes-plugin",     "title": "构建 Hermes 插件"},
    {"id": "guides/automate-with-cron",        "title": "用 Cron 自动化"},
    {"id": "guides/automation-templates",      "title": "自动化模板"},
    {"id": "guides/cron-troubleshooting",      "title": "Cron 排错"},
    {"id": "guides/work-with-skills",          "title": "Skills 实战"},
    {"id": "guides/delegation-patterns",       "title": "子代理委派模式"},
    {"id": "guides/github-pr-review-agent",    "title": "GitHub PR Review Agent"},
    {"id": "guides/webhook-github-pr-review",  "title": "Webhook 驱动 PR Review"},
    {"id": "guides/migrate-from-openclaw",     "title": "从 OpenClaw 迁移"},
    {"id": "guides/aws-bedrock",               "title": "AWS Bedrock"}
  ]},
  {"type": "category", "label": "开发者指南", "items": [
    {"id": "developer-guide/contributing", "title": "参与贡献"},
    {"type": "category", "label": "架构", "items": [
      {"id": "developer-guide/architecture",                    "title": "系统架构"},
      {"id": "developer-guide/agent-loop",                      "title": "Agent 主循环"},
      {"id": "developer-guide/prompt-assembly",                 "title": "Prompt 组装"},
      {"id": "developer-guide/context-compression-and-caching", "title": "上下文压缩与缓存"},
      {"id": "developer-guide/gateway-internals",               "title": "Gateway 内部"},
      {"id": "developer-guide/session-storage",                 "title": "会话存储"},
      {"id": "developer-guide/provider-runtime",                "title": "Provider 运行时"}
    ]},
    {"type": "category", "label": "扩展", "items": [
      {"id": "developer-guide/adding-tools",             "title": "新增工具"},
      {"id": "developer-guide/adding-providers",         "title": "新增 Provider"},
      {"id": "developer-guide/adding-platform-adapters", "title": "新增平台适配器"},
      {"id": "developer-guide/memory-provider-plugin",   "title": "记忆后端插件"},
      {"id": "developer-guide/context-engine-plugin",    "title": "Context Engine 插件"},
      {"id": "developer-guide/creating-skills",          "title": "编写 Skill"},
      {"id": "developer-guide/extending-the-cli",        "title": "扩展 CLI"}
    ]},
    {"type": "category", "label": "内部机制", "items": [
      {"id": "developer-guide/tools-runtime",     "title": "工具运行时"},
      {"id": "developer-guide/acp-internals",     "title": "ACP 内部"},
      {"id": "developer-guide/cron-internals",    "title": "Cron 内部"},
      {"id": "developer-guide/environments",      "title": "Environments"},
      {"id": "developer-guide/trajectory-format", "title": "Trajectory 格式"}
    ]}
  ]},
  {"type": "category", "label": "参考手册", "items": [
    {"id": "reference/cli-commands",             "title": "CLI 命令参考"},
    {"id": "reference/slash-commands",           "title": "斜杠命令参考"},
    {"id": "reference/profile-commands",         "title": "Profile 命令"},
    {"id": "reference/environment-variables",    "title": "环境变量"},
    {"id": "reference/tools-reference",          "title": "工具参考"},
    {"id": "reference/toolsets-reference",       "title": "工具集参考"},
    {"id": "reference/mcp-config-reference",     "title": "MCP 配置参考"},
    {"id": "reference/skills-catalog",           "title": "内置 Skills 清单"},
    {"id": "reference/optional-skills-catalog",  "title": "可选 Skills 清单"},
    {"id": "reference/faq",                      "title": "FAQ 与故障排查"}
  ]}
]
</script>

<section data-page="home">
  <h1>Hermes Agent 文档</h1>
  <p>由 <a href="https://nousresearch.com" target="_blank" rel="noopener">Nous Research</a> 打造的自我改进型 AI 智能体（self-improving AI agent）。它是少数内置"学习闭环"的 Agent——会从日常使用中<strong>创建 Skill</strong>，在使用过程中不断<strong>改进自己</strong>，<strong>主动持久化知识</strong>，<strong>检索自己的历史会话</strong>，并跨会话<strong>构建一个越来越准确的你的模型</strong>。</p>

  <div class="hd-tip"><strong>📌 关于本页</strong>这是 <a href="https://hermes-agent.nousresearch.com/docs/" target="_blank" rel="noopener">hermes-agent.nousresearch.com/docs/</a> 的简体中文版。左侧是完整目录树，单击任一条目即可在右侧切换页面；URL 的 <code>#/&lt;页面 id&gt;</code> hash 支持直接分享深链接。专有名词、命令、配置与代码保持英文原样，以保证可直接复制运行。核心章节（快速开始 / 安装 / CLI / 配置 / 安全 / 功能总览 / Tools / Skills / Memory / MCP / Voice Mode / Personality / Context Files / Messaging 总览 / Telegram / Architecture / FAQ / CLI 命令参考 / Tips）为完整翻译；其余章节提供中文概述 + 关键命令/配置 + 英文原文链接。</div>

  <h2>它是什么？</h2>
  <p>Hermes Agent 不是一个绑死在 IDE 上的 Coding Copilot，也不是包一层 API 的 Chatbot。它是一个<strong>自治代理（autonomous agent）</strong>，跑得越久越强。它能住在任何你把它放的地方——5 美元一个月的 VPS、GPU 集群、或空闲时几乎零成本的 Serverless 基础设施（Daytona、Modal）。你可以从 Telegram 跟它聊天，而它实际跑在一台你从不 SSH 的云 VM 上——它不绑你的笔记本。</p>

  <h2>常用入口</h2>
  <div class="hd-cards">
    <a class="hd-card" href="#/getting-started/installation" data-page="getting-started/installation"><span class="hd-card-title">🚀 安装</span><span class="hd-card-desc">60 秒一键安装到 Linux / macOS / WSL2</span></a>
    <a class="hd-card" href="#/getting-started/quickstart" data-page="getting-started/quickstart"><span class="hd-card-title">📖 快速上手</span><span class="hd-card-desc">第一次对话 + 必试的核心能力</span></a>
    <a class="hd-card" href="#/getting-started/learning-path" data-page="getting-started/learning-path"><span class="hd-card-title">🗺️ 学习路径</span><span class="hd-card-desc">按经验水平挑选该读哪些页面</span></a>
    <a class="hd-card" href="#/user-guide/configuration" data-page="user-guide/configuration"><span class="hd-card-title">⚙️ 配置</span><span class="hd-card-desc">config.yaml、provider、模型、各种选项</span></a>
    <a class="hd-card" href="#/user-guide/messaging/index" data-page="user-guide/messaging/index"><span class="hd-card-title">💬 Messaging Gateway</span><span class="hd-card-desc">接入 Telegram / Discord / Slack / WhatsApp</span></a>
    <a class="hd-card" href="#/user-guide/features/tools" data-page="user-guide/features/tools"><span class="hd-card-title">🔧 Tools & Toolsets</span><span class="hd-card-desc">47 个内置工具及平台级配置</span></a>
    <a class="hd-card" href="#/user-guide/features/memory" data-page="user-guide/features/memory"><span class="hd-card-title">🧠 Memory 系统</span><span class="hd-card-desc">跨会话持久化的 MEMORY.md 与 USER.md</span></a>
    <a class="hd-card" href="#/user-guide/features/skills" data-page="user-guide/features/skills"><span class="hd-card-title">📚 Skills 系统</span><span class="hd-card-desc">Agent 自动创建并复用的过程性记忆</span></a>
    <a class="hd-card" href="#/user-guide/features/mcp" data-page="user-guide/features/mcp"><span class="hd-card-title">🔌 MCP 集成</span><span class="hd-card-desc">接入任何 MCP Server，并可按服务器过滤工具</span></a>
    <a class="hd-card" href="#/user-guide/features/voice-mode" data-page="user-guide/features/voice-mode"><span class="hd-card-title">🎙️ Voice Mode</span><span class="hd-card-desc">CLI、Telegram、Discord VC 的实时语音</span></a>
    <a class="hd-card" href="#/user-guide/features/personality" data-page="user-guide/features/personality"><span class="hd-card-title">🎭 Personality / SOUL.md</span><span class="hd-card-desc">用 SOUL.md 定义默认人格</span></a>
    <a class="hd-card" href="#/user-guide/security" data-page="user-guide/security"><span class="hd-card-title">🔒 安全</span><span class="hd-card-desc">危险命令审批、授权、容器隔离</span></a>
    <a class="hd-card" href="#/guides/tips" data-page="guides/tips"><span class="hd-card-title">💡 使用技巧</span><span class="hd-card-desc">快速获得生产力的若干小窍门</span></a>
    <a class="hd-card" href="#/developer-guide/architecture" data-page="developer-guide/architecture"><span class="hd-card-title">🏗️ 架构</span><span class="hd-card-desc">Hermes 内部是怎么工作的</span></a>
    <a class="hd-card" href="#/reference/faq" data-page="reference/faq"><span class="hd-card-title">❓ FAQ</span><span class="hd-card-desc">常见问题与排查</span></a>
  </div>

  <h2>核心能力一览</h2>
  <ul>
    <li><strong>闭环的学习循环</strong> —— Agent 自己管理的记忆 + 周期性"自推自省"（self-nudge），自动创建 Skill、使用过程中自我改进，基于 FTS5 的跨会话检索配合 LLM 摘要，加 <a href="https://github.com/plastic-labs/honcho" target="_blank" rel="noopener">Honcho</a> 辩证式用户建模。</li>
    <li><strong>不只跑在你的笔记本上</strong> —— 6 种 terminal 后端：<code>local</code>、<code>docker</code>、<code>ssh</code>、<code>daytona</code>、<code>singularity</code>、<code>modal</code>。Daytona 和 Modal 提供 Serverless 持久化：空闲时休眠，几乎零成本。</li>
    <li><strong>住你常用的地方</strong> —— CLI、Telegram、Discord、Slack、WhatsApp、Signal、Matrix、Mattermost、Email、SMS、DingTalk、Feishu、WeCom、BlueBubbles、Home Assistant 等 15+ 平台，全部由一个 Gateway 统一托管。</li>
    <li><strong>由模型训练者打造</strong> —— 来自 Nous Research（Hermes、Nomos、Psyche 的背后团队）。可与 <a href="https://portal.nousresearch.com" target="_blank" rel="noopener">Nous Portal</a>、<a href="https://openrouter.ai" target="_blank" rel="noopener">OpenRouter</a>、OpenAI 或任意 OpenAI 兼容端点一起使用。</li>
    <li><strong>定时自动化</strong> —— 内置 cron，结果可投递到任何一个接入的平台。</li>
    <li><strong>委派 & 并行</strong> —— 可派发上下文隔离的子代理并行处理多条工作流。<code>execute_code</code> 的 Programmatic Tool Calling 能把多步管道压缩到一次推理里。</li>
    <li><strong>开放标准 Skill</strong> —— 兼容 <a href="https://agentskills.io" target="_blank" rel="noopener">agentskills.io</a>，Skill 可移植、可分享，社区通过 Skills Hub 贡献。</li>
    <li><strong>全面的 Web 能力</strong> —— 搜索、正文抽取、浏览、视觉、图像生成、TTS。</li>
    <li><strong>MCP 支持</strong> —— 可连接任意 MCP Server 扩展工具能力。</li>
    <li><strong>面向研究</strong> —— 批处理、trajectory 导出、基于 Atropos 的 RL 训练。</li>
  </ul>
</section>

<!-- =========================================================================
  Getting Started
  ========================================================================= -->

<section data-page="getting-started/quickstart">
  <h1>快速上手（Quickstart）</h1>
  <p>这份指南会把你从零带到"一个可经受真实使用的 Hermes 安装"。完成安装、选好 provider、验证聊天可用，然后清楚地知道出问题时该怎么办。</p>

  <h2>适合谁读</h2>
  <ul>
    <li>完全零基础，想要最短的"可用"路径</li>
    <li>正在切换 provider，不想因为配置错误而浪费时间</li>
    <li>要给团队、机器人、长驻工作流搭 Hermes</li>
    <li>受够了"装完了但就是啥也不干"的状态</li>
  </ul>

  <h2>最快路径</h2>
  <p>挑一条与你目标匹配的：</p>
  <table>
    <thead><tr><th>目标</th><th>先做</th><th>再做</th></tr></thead>
    <tbody>
      <tr><td>只是想让 Hermes 在本机跑起来</td><td><code>hermes setup</code></td><td>真发一条消息，验证它会回复</td></tr>
      <tr><td>已经知道要用哪个 provider</td><td><code>hermes model</code></td><td>保存配置，开始聊天</td></tr>
      <tr><td>想做 Bot 或常驻服务</td><td>CLI 跑通后再 <code>hermes gateway setup</code></td><td>接上 Telegram / Discord / Slack 等</td></tr>
      <tr><td>要用本地或自建模型</td><td><code>hermes model</code> → 自定义 endpoint</td><td>验证 endpoint、模型名、上下文长度</td></tr>
      <tr><td>想要多 provider 容灾</td><td>先 <code>hermes model</code></td><td>基础聊天通了之后再加路由和 fallback</td></tr>
    </tbody>
  </table>

  <div class="hd-note"><strong>经验法则</strong>：如果 Hermes 连一次正常聊天都完成不了，就先别急着加其他功能。先把一次干净的对话跑通，之后再叠加 gateway、cron、skills、voice、routing。</div>

  <hr>

  <h2>1. 安装 Hermes Agent</h2>
  <p>一行安装器：</p>
  <pre><code># Linux / macOS / WSL2 / Android (Termux)
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash</code></pre>

  <div class="hd-tip"><strong>Android / Termux</strong>：如果装在手机上，见 <a data-page="getting-started/termux">Termux 指南</a>，里面有已测试的手动步骤、支持的 extras 以及当前 Android 特定的限制。</div>
  <div class="hd-tip"><strong>Windows 用户</strong>：先装 <a href="https://learn.microsoft.com/en-us/windows/wsl/install" target="_blank" rel="noopener">WSL2</a>，然后在 WSL2 终端里执行上面的命令。</div>

  <p>安装结束后 reload 一下 shell：</p>
  <pre><code>source ~/.bashrc   # 或 source ~/.zshrc</code></pre>

  <p>更多安装选项、前置条件、故障排查见 <a data-page="getting-started/installation">安装</a> 页。</p>

  <h2>2. 选一个 Provider</h2>
  <p>这是最关键的一步。用 <code>hermes model</code> 以交互方式选：</p>
  <pre><code>hermes model</code></pre>
  <p>推荐默认：</p>
  <table>
    <thead><tr><th>场景</th><th>推荐路径</th></tr></thead>
    <tbody>
      <tr><td>希望配置最少</td><td>Nous Portal 或 OpenRouter</td></tr>
      <tr><td>手上已经有 Claude 或 Codex 的登录</td><td>Anthropic 或 OpenAI Codex</td></tr>
      <tr><td>想本地 / 私有推理</td><td>Ollama，或任意 OpenAI 兼容 endpoint</td></tr>
      <tr><td>想要多 provider 路由</td><td>OpenRouter</td></tr>
      <tr><td>自己有 GPU 服务器</td><td>vLLM / SGLang / LiteLLM / 任意 OpenAI 兼容 endpoint</td></tr>
    </tbody>
  </table>
  <p>首次使用的建议：选一个 provider，除非你有充分理由，否则直接接受默认值。完整 provider 目录及所需环境变量见 <a data-page="integrations/providers">AI Providers</a>。</p>

  <div class="hd-caution"><strong>最低上下文：64K tokens</strong>。Hermes 要求模型至少 <strong>64,000</strong> tokens 的上下文窗口，否则启动时会被拒绝。主流托管模型（Claude、GPT、Gemini、Qwen、DeepSeek）都轻松达标。跑本地模型时把 context 设到至少 64K，例如 llama.cpp 的 <code>--ctx-size 65536</code> 或 Ollama 的 <code>-c 65536</code>。</div>

  <div class="hd-tip">你可以随时用 <code>hermes model</code> 切换 provider，没有锁定。</div>

  <h3>配置存在哪里</h3>
  <p>Hermes 把 secret 与普通配置分开存：</p>
  <ul>
    <li><strong>Secret 与 token</strong> → <code>~/.hermes/.env</code></li>
    <li><strong>非 secret 的设置</strong> → <code>~/.hermes/config.yaml</code></li>
  </ul>
  <p>最方便的设置方式是通过 CLI：</p>
  <pre><code>hermes config set model anthropic/claude-opus-4.6
hermes config set terminal.backend docker
hermes config set OPENROUTER_API_KEY sk-or-...</code></pre>
  <p>对应的值会自动写到正确的文件。</p>

  <h2>3. 开始第一次聊天</h2>
  <pre><code>hermes            # 经典 CLI
hermes --tui      # 现代 TUI（推荐）</code></pre>
  <p>你会看到一个欢迎横幅，列出当前模型、可用工具和 skills。用一条"具体且容易验证"的 prompt：</p>
  <pre><code>用 5 条 bullet 总结当前仓库，并告诉我 main entrypoint 是哪个文件。</code></pre>
  <pre><code>看一下我当前目录，告诉我哪个文件看起来是项目主文件。</code></pre>
  <pre><code>帮我给这个代码仓搭一套干净的 GitHub PR 流程。</code></pre>
  <p><strong>"成功"的样子</strong>：横幅显示你选的 model/provider；Hermes 无错误地回复；需要时它会调用工具（terminal、读文件、web search）；多轮对话正常延续。</p>

  <h2>4. 验证会话恢复</h2>
  <pre><code>hermes --continue    # 恢复最近一次会话
hermes -c            # 同上，短写法</code></pre>
  <p>如果没能回到你刚才的会话，检查是不是换了 profile，或会话根本没保存。多机 / 多配置场景下，这一步很重要。</p>

  <h2>5. 试试关键特性</h2>
  <h3>让它用终端</h3>
  <pre><code>❯ 我的磁盘占用怎么样？列出占用最大的 5 个目录。</code></pre>
  <h3>斜杠命令</h3>
  <p>输入 <code>/</code> 会弹出所有命令的自动补全：</p>
  <table>
    <thead><tr><th>命令</th><th>作用</th></tr></thead>
    <tbody>
      <tr><td><code>/help</code></td><td>列出全部命令</td></tr>
      <tr><td><code>/tools</code></td><td>列出可用工具</td></tr>
      <tr><td><code>/model</code></td><td>交互式切换模型</td></tr>
      <tr><td><code>/personality pirate</code></td><td>来个好玩的 personality</td></tr>
      <tr><td><code>/save</code></td><td>保存对话</td></tr>
    </tbody>
  </table>
  <h3>多行输入</h3>
  <p>按 <strong>Alt+Enter</strong> 或 <strong>Ctrl+J</strong> 新起一行，适合粘贴代码或写长 prompt。</p>
  <h3>打断 Agent</h3>
  <p>如果 Agent 跑太久，直接再发一条消息回车 —— 它会打断当前任务，切换到你的新指令。<code>Ctrl+C</code> 也能打断。</p>

  <h2>6. 加一层</h2>
  <p>只有基础聊天能跑了，才去叠加这些。按需挑一项：</p>
  <h3>Bot 或共享助手</h3>
  <pre><code>hermes gateway setup    # 交互式配置各平台</code></pre>
  <p>可接入 <a data-page="user-guide/messaging/telegram">Telegram</a>、<a data-page="user-guide/messaging/discord">Discord</a>、<a data-page="user-guide/messaging/slack">Slack</a>、<a data-page="user-guide/messaging/whatsapp">WhatsApp</a>、<a data-page="user-guide/messaging/signal">Signal</a>、<a data-page="user-guide/messaging/email">Email</a>、<a data-page="user-guide/messaging/homeassistant">Home Assistant</a>。</p>
  <h3>自动化与工具</h3>
  <ul>
    <li><code>hermes tools</code> —— 按平台调整工具权限</li>
    <li><code>hermes skills</code> —— 浏览并安装可复用的工作流</li>
    <li>Cron —— 等 Bot 或 CLI 稳定之后再开</li>
  </ul>
  <h3>沙箱化 terminal</h3>
  <pre><code>hermes config set terminal.backend docker    # Docker 隔离
hermes config set terminal.backend ssh       # 远端服务器</code></pre>
  <h3>Voice Mode</h3>
  <pre><code>pip install "hermes-agent[voice]"
# 内含 faster-whisper，可免费本地做 STT</code></pre>
  <p>CLI 里 <code>/voice on</code>，按 <code>Ctrl+B</code> 录音。见 <a data-page="user-guide/features/voice-mode">Voice Mode</a>。</p>
  <h3>Skills</h3>
  <pre><code>hermes skills search kubernetes
hermes skills install openai/skills/k8s</code></pre>
  <p>或在会话中 <code>/skills</code>。</p>
  <h3>MCP 服务器</h3>
  <pre><code># 加到 ~/.hermes/config.yaml
mcp_servers:
  github:
    command: npx
    args: ["-y", "@modelcontextprotocol/server-github"]
    env:
      GITHUB_PERSONAL_ACCESS_TOKEN: "ghp_xxx"</code></pre>
  <h3>编辑器集成（ACP）</h3>
  <pre><code>pip install -e '.[acp]'
hermes acp</code></pre>
  <p>见 <a data-page="user-guide/features/acp">ACP 编辑器集成</a>。</p>

  <hr>

  <h2>常见故障模式</h2>
  <p>这些问题最容易浪费你时间：</p>
  <table>
    <thead><tr><th>现象</th><th>可能原因</th><th>修复</th></tr></thead>
    <tbody>
      <tr><td>Hermes 打开但回复是空/乱的</td><td>Provider 认证或模型选错</td><td>重跑 <code>hermes model</code> 确认 provider/model/auth</td></tr>
      <tr><td>自定义 endpoint "能跑但返回垃圾"</td><td>Base URL / 模型名错，或不是真正的 OpenAI 兼容</td><td>先在另一个客户端里独立验证 endpoint</td></tr>
      <tr><td>Gateway 启动了但谁都发不了消息</td><td>Bot token / allowlist / 平台配置没做完</td><td>重跑 <code>hermes gateway setup</code>，看 <code>hermes gateway status</code></td></tr>
      <tr><td><code>hermes --continue</code> 找不到旧会话</td><td>换了 profile，或会话没保存</td><td><code>hermes sessions list</code>，确认在同一 profile</td></tr>
      <tr><td>模型不可用 / fallback 行为奇怪</td><td>路由或 fallback 设置太激进</td><td>基础 provider 稳定前先别开路由</td></tr>
      <tr><td><code>hermes doctor</code> 报配置问题</td><td>配置缺项或陈旧</td><td>修好后先跑一次纯聊天再加功能</td></tr>
    </tbody>
  </table>

  <h2>恢复工具箱</h2>
  <p>感觉哪里不对时，按这个顺序走一遍：</p>
  <ol>
    <li><code>hermes doctor</code></li>
    <li><code>hermes model</code></li>
    <li><code>hermes setup</code></li>
    <li><code>hermes sessions list</code></li>
    <li><code>hermes --continue</code></li>
    <li><code>hermes gateway status</code></li>
  </ol>
  <p>大多数"好像坏了"的状态都能快速回到已知稳定点。</p>

  <h2>速查</h2>
  <table>
    <thead><tr><th>命令</th><th>作用</th></tr></thead>
    <tbody>
      <tr><td><code>hermes</code></td><td>开始聊天</td></tr>
      <tr><td><code>hermes model</code></td><td>选 provider 与模型</td></tr>
      <tr><td><code>hermes tools</code></td><td>按平台配置工具</td></tr>
      <tr><td><code>hermes setup</code></td><td>完整安装向导</td></tr>
      <tr><td><code>hermes doctor</code></td><td>自检</td></tr>
      <tr><td><code>hermes update</code></td><td>升级</td></tr>
      <tr><td><code>hermes gateway</code></td><td>启动消息网关</td></tr>
      <tr><td><code>hermes --continue</code></td><td>恢复最近一次会话</td></tr>
    </tbody>
  </table>

  <h2>下一步</h2>
  <ul>
    <li><a data-page="user-guide/cli">CLI 指南</a> —— 把终端用精</li>
    <li><a data-page="user-guide/configuration">配置</a> —— 个性化</li>
    <li><a data-page="user-guide/messaging/index">Messaging Gateway</a> —— 接各类 IM</li>
    <li><a data-page="user-guide/features/tools">Tools & Toolsets</a></li>
    <li><a data-page="integrations/providers">AI Providers</a></li>
    <li><a data-page="user-guide/features/skills">Skills 系统</a></li>
    <li><a data-page="guides/tips">使用技巧与最佳实践</a></li>
  </ul>
</section>

<section data-page="getting-started/installation">
  <h1>安装</h1>
  <p>用一行安装器在两分钟内跑起来 Hermes Agent。</p>

  <h2>一键安装</h2>
  <h3>Linux / macOS / WSL2</h3>
  <pre><code>curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash</code></pre>

  <h3>Android / Termux</h3>
  <p>Hermes 现在也提供识别 Termux 的安装路径：</p>
  <pre><code>curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash</code></pre>
  <p>安装器自动识别 Termux 并切到已测试的 Android 流程：</p>
  <ul>
    <li>用 Termux 的 <code>pkg</code> 装系统依赖（<code>git</code>、<code>python</code>、<code>nodejs</code>、<code>ripgrep</code>、<code>ffmpeg</code>、编译工具）</li>
    <li>用 <code>python -m venv</code> 建 virtualenv</li>
    <li>自动设置 <code>ANDROID_API_LEVEL</code> 以支持 Android wheel 编译</li>
    <li>用 <code>pip</code> 安装精选的 <code>.[termux]</code> extras</li>
    <li>默认跳过未经测试的 browser / WhatsApp 引导</li>
  </ul>
  <p>如果需要显式走全量步骤，见 <a data-page="getting-started/termux">Termux 指南</a>。</p>

  <div class="hd-warn"><strong>Windows</strong>：原生 Windows <strong>不支持</strong>。请先装 <a href="https://learn.microsoft.com/en-us/windows/wsl/install" target="_blank" rel="noopener">WSL2</a>，然后在 WSL2 里运行上面的命令。</div>

  <h3>安装器做了什么</h3>
  <p>全部自动：所有依赖（Python、Node.js、ripgrep、ffmpeg）、仓库克隆、virtualenv、全局 <code>hermes</code> 命令、LLM provider 配置。结束时就可以直接开聊。</p>

  <h3>安装之后</h3>
  <pre><code>source ~/.bashrc   # 或 source ~/.zshrc
hermes             # 开始聊！</code></pre>
  <p>之后想单独改配置：</p>
  <pre><code>hermes model          # 选 LLM provider 与模型
hermes tools          # 配置启用哪些工具
hermes gateway setup  # 配置消息平台
hermes config set     # 设置单个配置值
hermes setup          # 或一键走完整向导</code></pre>

  <hr>

  <h2>前置条件</h2>
  <p>唯一必须的前置是 <strong>Git</strong>。其它都由安装器搞定：</p>
  <ul>
    <li><strong>uv</strong>（高速 Python 包管理器）</li>
    <li><strong>Python 3.11</strong>（通过 uv，不需要 sudo）</li>
    <li><strong>Node.js v22</strong>（用于浏览器自动化与 WhatsApp 桥接）</li>
    <li><strong>ripgrep</strong>（快速文件搜索）</li>
    <li><strong>ffmpeg</strong>（TTS 音频格式转换）</li>
  </ul>
  <div class="hd-info">你<strong>不需要</strong>手工装 Python / Node.js / ripgrep / ffmpeg。安装器会检测并补齐。只要保证 <code>git --version</code> 能跑就行。</div>
  <div class="hd-tip"><strong>Nix 用户</strong>：有专门的 Nix flake + NixOS module + 可选容器模式。见 <a data-page="getting-started/nix-setup">Nix & NixOS 安装</a>。</div>

  <h2>手动安装</h2>
  <p>如果你偏好手工步骤（适合 contributor 或需要细调环境）：</p>
  <pre><code>git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
pip install uv
uv venv
source .venv/bin/activate
uv pip install -e ".[all]"
./hermes setup</code></pre>
  <p>可选 extras：<code>.[voice]</code>、<code>.[messaging]</code>、<code>.[tts-premium]</code>、<code>.[acp]</code>、<code>.[mcp]</code>、<code>.[termux]</code>、<code>.[all]</code>。</p>

  <h2>升级</h2>
  <pre><code>hermes update</code></pre>
  <p>详见 <a data-page="getting-started/updating">升级</a>。</p>

  <h2>排错</h2>
  <ul>
    <li>安装中断：重跑安装命令即可，是幂等的</li>
    <li>权限错误：不要 <code>sudo bash</code>，按普通用户跑</li>
    <li>Python 版本不兼容：使用安装器自带的 uv-managed Python 3.11</li>
    <li>更多问题：<code>hermes doctor</code> 和 <a data-page="reference/faq">FAQ</a></li>
  </ul>
  <p>本页为英文原文 <a href="https://hermes-agent.nousresearch.com/docs/getting-started/installation/" target="_blank" rel="noopener">Installation</a> 的完整翻译。</p>
</section>

<section data-page="getting-started/termux">
  <h1>Termux / Android 安装</h1>
  <p>Hermes 在 Android 上通过 <a href="https://termux.dev" target="_blank" rel="noopener">Termux</a> 有一套已测试的安装路径。</p>
  <h2>一键安装</h2>
  <pre><code>curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash</code></pre>
  <p>安装器会自动识别 Termux 并走 Android 专用流程（见 <a data-page="getting-started/installation">安装</a> 页的 Termux 一节）。</p>

  <h2>手动步骤（如果需要）</h2>
  <pre><code>pkg update &amp;&amp; pkg install -y git python nodejs ripgrep ffmpeg build-essential
git clone https://github.com/NousResearch/hermes-agent.git ~/.hermes/hermes-agent
cd ~/.hermes/hermes-agent
python -m venv .venv
source .venv/bin/activate
pip install --upgrade pip wheel
export ANDROID_API_LEVEL=24
pip install -e ".[termux]"
./hermes setup</code></pre>

  <h2>Android 上的限制</h2>
  <ul>
    <li><code>.[all]</code> 在 Android 上 <strong>不可用</strong>：<code>voice</code> extra 依赖 <code>faster-whisper</code> → <code>ctranslate2</code>，后者没发布 Android wheel。用 <code>.[termux]</code> 代替。</li>
    <li>浏览器自动化与 WhatsApp 桥接默认跳过，手机上运行这些不稳定。</li>
    <li>Docker 后端不可用（Termux 没有 Docker 守护进程），可以改用 <code>ssh</code> 后端连到一台服务器。</li>
  </ul>

  <h2>推荐搭配</h2>
  <ul>
    <li>把 terminal 后端设为 <code>ssh</code> 或 <code>daytona</code>，把真正的执行放到云上</li>
    <li>把 Bot 接到 Telegram / Discord，用 <code>hermes gateway</code> 在手机后台跑</li>
  </ul>
  <p>完整原文：<a href="https://hermes-agent.nousresearch.com/docs/getting-started/termux/" target="_blank" rel="noopener">Termux Installation</a>。</p>
</section>

<section data-page="getting-started/nix-setup">
  <h1>Nix & NixOS 安装</h1>
  <p>Hermes 为 Nix 用户（NixOS、nix-darwin、任何有 Nix 的 Linux/macOS）提供了一条声明式路径：</p>
  <ul>
    <li><strong>Nix flake</strong> —— <code>nix run github:NousResearch/hermes-agent</code> 直接跑</li>
    <li><strong>NixOS module</strong> —— 以声明式服务方式安装 Gateway、Cron、API Server</li>
    <li><strong>Container mode</strong>（可选）—— 把 Hermes 封进一个 OCI 容器，系统里只保留 entrypoint</li>
  </ul>

  <h2>用 flake 快速试用</h2>
  <pre><code>nix run github:NousResearch/hermes-agent -- --help
nix run github:NousResearch/hermes-agent -- chat</code></pre>

  <h2>加到 NixOS configuration</h2>
  <pre><code>{
  inputs.hermes.url = "github:NousResearch/hermes-agent";
  outputs = { nixpkgs, hermes, ... }: {
    nixosConfigurations.myhost = nixpkgs.lib.nixosSystem {
      modules = [
        hermes.nixosModules.default
        ({ ... }: {
          services.hermes-agent.enable = true;
          services.hermes-agent.gateway.enable = true;
        })
      ];
    };
  };
}</code></pre>

  <h2>声明式配置</h2>
  <p>所有 <code>config.yaml</code> 里的值都可以通过 <code>services.hermes-agent.settings</code> 声明。Secret 建议走 <code>age</code> / <code>sops-nix</code> 注入到 <code>~/.hermes/.env</code>。</p>
  <p>完整细节请看英文 <a href="https://hermes-agent.nousresearch.com/docs/getting-started/nix-setup/" target="_blank" rel="noopener">Nix & NixOS Setup</a>。</p>
</section>

<section data-page="getting-started/updating">
  <h1>升级 Hermes</h1>
  <p>推荐方式：</p>
  <pre><code>hermes update</code></pre>
  <p>这会 <code>git pull</code> 最新代码并重装依赖（保留 virtualenv）。</p>

  <h2>会话内升级</h2>
  <p>在任何会话里（CLI 或 messaging）都可以：</p>
  <pre><code>/update</code></pre>

  <h2>手动升级</h2>
  <pre><code>cd ~/.hermes/hermes-agent
git pull
source .venv/bin/activate
uv pip install -e ".[all]" --upgrade</code></pre>

  <h2>检查配置是否需要迁移</h2>
  <pre><code>hermes config check     # 列出新加但未设置的选项
hermes config migrate   # 交互式补全</code></pre>

  <h2>回滚</h2>
  <p>想固定在某个版本：<code>git checkout &lt;tag&gt;</code>，然后重装依赖。</p>
  <p>英文原文：<a href="https://hermes-agent.nousresearch.com/docs/getting-started/updating/" target="_blank" rel="noopener">Updating Hermes</a>。</p>
</section>

<section data-page="getting-started/learning-path">
  <h1>学习路径</h1>
  <p>Hermes Agent 能做的事很多 —— CLI 助手、Telegram/Discord bot、任务自动化、RL 训练数据生成……这一页帮你根据经验水平和目标，决定从哪里开始、该读哪些文档。</p>

  <div class="hd-tip"><strong>Start Here</strong>：如果还没装好，先看 <a data-page="getting-started/installation">安装</a> 再跑一遍 <a data-page="getting-started/quickstart">快速上手</a>。下面内容都假设你已经有一个能跑的 Hermes。</div>

  <h2>怎么用这一页</h2>
  <ul>
    <li><strong>知道自己是什么水平</strong>？跳到下面的"按经验水平"表，按顺序读。</li>
    <li><strong>有明确目标</strong>？跳到"按使用场景"，找到匹配的那一栏。</li>
    <li><strong>就是逛逛</strong>？看"核心能力一览"快速扫一遍。</li>
  </ul>

  <h2>按经验水平</h2>
  <table>
    <thead><tr><th>水平</th><th>目标</th><th>推荐阅读</th><th>预估时间</th></tr></thead>
    <tbody>
      <tr><td><strong>入门</strong></td><td>跑起来，能基础对话，用内置工具</td><td><a data-page="getting-started/installation">安装</a> → <a data-page="getting-started/quickstart">快速上手</a> → <a data-page="user-guide/cli">CLI</a> → <a data-page="user-guide/configuration">配置</a></td><td>约 1 小时</td></tr>
      <tr><td><strong>进阶</strong></td><td>搭好 bot，用上 memory / cron / skills 等高级功能</td><td><a data-page="user-guide/sessions">会话</a> → <a data-page="user-guide/messaging/index">Messaging</a> → <a data-page="user-guide/features/tools">Tools</a> → <a data-page="user-guide/features/skills">Skills</a> → <a data-page="user-guide/features/memory">Memory</a> → <a data-page="user-guide/features/cron">Cron</a></td><td>2–3 小时</td></tr>
      <tr><td><strong>高级</strong></td><td>写自定义 tool / skill、做 RL 训练、参与贡献</td><td><a data-page="developer-guide/architecture">Architecture</a> → <a data-page="developer-guide/adding-tools">新增工具</a> → <a data-page="developer-guide/creating-skills">编写 Skill</a> → <a data-page="user-guide/features/rl-training">RL Training</a> → <a data-page="developer-guide/contributing">贡献</a></td><td>4–6 小时</td></tr>
    </tbody>
  </table>

  <h2>按使用场景</h2>

  <h3>"我想要一个 CLI Coding 助手"</h3>
  <ol>
    <li><a data-page="getting-started/installation">安装</a></li>
    <li><a data-page="getting-started/quickstart">快速上手</a></li>
    <li><a data-page="user-guide/cli">CLI 使用</a></li>
    <li><a data-page="user-guide/features/code-execution">Code Execution</a></li>
    <li><a data-page="user-guide/features/context-files">Context Files</a></li>
    <li><a data-page="guides/tips">Tips & Tricks</a></li>
  </ol>

  <h3>"我想要一个 Telegram / Discord Bot"</h3>
  <ol>
    <li><a data-page="getting-started/installation">安装</a></li>
    <li><a data-page="user-guide/configuration">配置</a></li>
    <li><a data-page="user-guide/messaging/index">Messaging 总览</a></li>
    <li><a data-page="user-guide/messaging/telegram">Telegram 配置</a></li>
    <li><a data-page="user-guide/messaging/discord">Discord 配置</a></li>
    <li><a data-page="user-guide/features/voice-mode">Voice Mode</a></li>
    <li><a data-page="guides/use-voice-mode-with-hermes">用好 Voice Mode</a></li>
    <li><a data-page="user-guide/security">Security</a></li>
  </ol>
  <p>完整项目示例：<a data-page="guides/daily-briefing-bot">每日简报机器人</a>、<a data-page="guides/team-telegram-assistant">团队 Telegram 助手</a>。</p>

  <h3>"我想做任务自动化"</h3>
  <ol>
    <li><a data-page="getting-started/quickstart">快速上手</a></li>
    <li><a data-page="user-guide/features/cron">Cron</a></li>
    <li><a data-page="user-guide/features/batch-processing">Batch Processing</a></li>
    <li><a data-page="user-guide/features/delegation">Delegation</a></li>
    <li><a data-page="user-guide/features/hooks">Hooks</a></li>
  </ol>

  <h3>"我想写自定义 tool / skill"</h3>
  <ol>
    <li><a data-page="developer-guide/architecture">架构</a></li>
    <li><a data-page="developer-guide/adding-tools">新增 Tool</a></li>
    <li><a data-page="developer-guide/creating-skills">编写 Skill</a></li>
    <li><a data-page="user-guide/features/skills">Skills 系统</a></li>
    <li><a data-page="guides/work-with-skills">Skills 实战</a></li>
  </ol>

  <h3>"我想把 Hermes 用在我自己的服务里"</h3>
  <ol>
    <li><a data-page="user-guide/features/api-server">API Server</a></li>
    <li><a data-page="user-guide/features/acp">ACP 编辑器集成</a></li>
    <li><a data-page="guides/python-library">作为 Python 库</a></li>
    <li><a data-page="user-guide/features/mcp">MCP 集成</a></li>
  </ol>

  <h3>"我想做 RL 训练 / 研究"</h3>
  <ol>
    <li><a data-page="user-guide/features/batch-processing">Batch Processing</a></li>
    <li><a data-page="user-guide/features/rl-training">RL Training</a></li>
    <li><a data-page="developer-guide/trajectory-format">Trajectory 格式</a></li>
  </ol>

  <h2>核心能力一览（速览表）</h2>
  <table>
    <thead><tr><th>能力</th><th>用途</th><th>去哪看</th></tr></thead>
    <tbody>
      <tr><td>CLI / TUI</td><td>终端交互</td><td><a data-page="user-guide/cli">CLI</a> / <a data-page="user-guide/tui">TUI</a></td></tr>
      <tr><td>Messaging</td><td>Telegram / Discord / Slack / …</td><td><a data-page="user-guide/messaging/index">总览</a></td></tr>
      <tr><td>Memory</td><td>跨会话记忆</td><td><a data-page="user-guide/features/memory">Memory</a></td></tr>
      <tr><td>Skills</td><td>按需加载的过程性知识</td><td><a data-page="user-guide/features/skills">Skills</a></td></tr>
      <tr><td>Cron</td><td>定时任务</td><td><a data-page="user-guide/features/cron">Cron</a></td></tr>
      <tr><td>MCP</td><td>接入外部工具生态</td><td><a data-page="user-guide/features/mcp">MCP</a></td></tr>
      <tr><td>Voice Mode</td><td>语音交互</td><td><a data-page="user-guide/features/voice-mode">Voice Mode</a></td></tr>
      <tr><td>Delegation</td><td>子代理并行</td><td><a data-page="user-guide/features/delegation">Delegation</a></td></tr>
      <tr><td>RL</td><td>trajectory + 训练</td><td><a data-page="user-guide/features/rl-training">RL Training</a></td></tr>
      <tr><td>Plugins</td><td>三类插件扩展</td><td><a data-page="user-guide/features/plugins">Plugins</a></td></tr>
    </tbody>
  </table>
  <p>本页为英文原文 <a href="https://hermes-agent.nousresearch.com/docs/getting-started/learning-path/" target="_blank" rel="noopener">Learning Path</a> 的完整翻译。</p>
</section>

<!-- =========================================================================
  Using Hermes
  ========================================================================= -->

<section data-page="user-guide/cli">
  <h1>CLI 命令行界面</h1>
  <p>Hermes Agent 的 CLI 是一个完整的终端用户界面 —— 不是 Web UI。支持多行编辑、斜杠命令补全、会话历史、打断并重定向、流式工具输出。为"住在终端里的人"设计。</p>

  <div class="hd-tip">Hermes 还有一个现代的 TUI（模态浮层、鼠标选区、非阻塞输入），用 <code>hermes --tui</code> 启动。见 <a data-page="user-guide/tui">TUI</a>。</div>

  <h2>启动方式</h2>
  <pre><code># 默认交互式
hermes

# 单次查询（非交互）
hermes chat -q "Hello"

# 指定模型
hermes chat --model "anthropic/claude-sonnet-4"

# 指定 provider
hermes chat --provider nous
hermes chat --provider openrouter

# 指定工具集
hermes chat --toolsets "web,terminal,skills"

# 预加载一个或多个 skill
hermes -s hermes-agent-dev,github-auth
hermes chat -s github-pr-workflow -q "open a draft PR"

# 恢复会话
hermes --continue              # 最近一次 CLI 会话（-c）
hermes --resume &lt;session_id&gt;   # 按 ID 恢复（-r）

# 调试输出
hermes chat --verbose

# 隔离 git worktree（多代理并行）
hermes -w
hermes -w -q "Fix issue #123"</code></pre>

  <h2>界面布局</h2>
  <p>启动后的欢迎横幅里会一眼看到：当前模型、terminal 后端、工作目录、可用工具、已安装的 skills。</p>

  <h3>状态栏</h3>
  <p>在输入区上方有一条会实时刷新的 status bar：model、已用 token、当前工作目录、激活的 profile 等。</p>

  <h2>斜杠命令</h2>
  <p>输入 <code>/</code> 按 Tab 自动补全；包含所有内置命令和已安装 skill。常用：</p>
  <ul>
    <li><code>/help</code> —— 所有命令</li>
    <li><code>/model</code> —— 切模型</li>
    <li><code>/provider</code> —— 列 provider 与认证状态</li>
    <li><code>/personality &lt;name&gt;</code> —— 切人格</li>
    <li><code>/compress</code> —— 手动压缩上下文</li>
    <li><code>/save</code>、<code>/title</code>、<code>/resume</code>、<code>/status</code></li>
    <li><code>/tools</code>、<code>/skills</code>、<code>/memory</code>、<code>/rollback</code></li>
    <li><code>/yolo</code> —— 切换危险命令自动放行（见 <a data-page="user-guide/security">Security</a>）</li>
    <li><code>/voice on|off|tts|join|leave|status</code></li>
    <li><code>/reasoning [level|show|hide]</code></li>
    <li><code>/reload-mcp</code>、<code>/update</code>、<code>/background &lt;prompt&gt;</code></li>
    <li><code>/&lt;skill-name&gt;</code> —— 直接调用任何已安装 skill</li>
  </ul>
  <p>完整列表见 <a data-page="reference/slash-commands">斜杠命令参考</a>。</p>

  <h2>按键绑定</h2>
  <table>
    <thead><tr><th>按键</th><th>作用</th></tr></thead>
    <tbody>
      <tr><td>Enter</td><td>发送</td></tr>
      <tr><td>Alt+Enter / Ctrl+J</td><td>换行（不发送）</td></tr>
      <tr><td>Ctrl+C（一次）</td><td>打断当前回答；可再输入新消息重定向</td></tr>
      <tr><td>Ctrl+C（2 秒内两次）</td><td>强制退出</td></tr>
      <tr><td>Ctrl+V</td><td>粘贴剪贴板图片（交给视觉模型分析）</td></tr>
      <tr><td>Ctrl+B</td><td>语音模式录音</td></tr>
      <tr><td>↑ / ↓</td><td>历史记录</td></tr>
      <tr><td>Tab</td><td>斜杠命令 / @ 引用补全</td></tr>
    </tbody>
  </table>

  <h2>@ 引用</h2>
  <p>输入 <code>@</code> 可以引用文件、目录、git diff、URL，例如：</p>
  <pre><code>@src/main.py 帮我重构这个文件
@git-diff  解释一下这次 diff 干了什么
@https://example.com/spec  按这个规范写单测</code></pre>
  <p>详见 <a data-page="user-guide/features/context-references">上下文引用</a>。</p>

  <h2>Personality / 外观</h2>
  <ul>
    <li><code>/personality</code> 切人格，见 <a data-page="user-guide/features/personality">Personality</a></li>
    <li>启动横幅、颜色、spinner 可定制，见 <a data-page="user-guide/features/skins">Skins 与主题</a></li>
  </ul>

  <h2>会话</h2>
  <p>每次 <code>hermes</code> 启动都是一个新的 CLI 会话，保存到 <code>~/.hermes/sessions/</code>。可以列出、恢复、改名、删除，见 <a data-page="user-guide/sessions">会话管理</a>。</p>

  <h2>接下来</h2>
  <ul>
    <li><a data-page="user-guide/tui">TUI</a> —— 更现代的终端 UI</li>
    <li><a data-page="user-guide/configuration">配置</a></li>
    <li><a data-page="user-guide/features/tools">Tools & Toolsets</a></li>
    <li><a data-page="reference/cli-commands">CLI 命令参考</a></li>
    <li><a data-page="reference/slash-commands">斜杠命令参考</a></li>
  </ul>
</section>

<section data-page="user-guide/tui">
  <h1>TUI 终端界面</h1>
  <p>Hermes 还提供一个更现代的 TUI（基于 TypeScript + 终端绘制），特性：</p>
  <ul>
    <li>模态浮层（命令、模型选择、会话列表、工具进度都是 overlay）</li>
    <li>鼠标选区 / 复制</li>
    <li>非阻塞输入（Agent 思考期间仍可打字 / 发送）</li>
    <li>更紧凑的 tool activity 展示</li>
  </ul>

  <h2>启动</h2>
  <pre><code>hermes --tui
# 等价：HERMES_TUI=1 hermes</code></pre>
  <p>TUI 与经典 CLI 共享 sessions、slash commands 与配置。建议两种都试试，挑顺手的。</p>

  <h2>开发者模式</h2>
  <p>用 <code>hermes --tui --dev</code> 通过 <code>tsx</code> 直接跑 TS 源码，方便给 TUI 做贡献。</p>

  <p>详细快捷键、组件、截图见英文原文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/tui/" target="_blank" rel="noopener">TUI</a>。</p>
</section>

<section data-page="user-guide/configuration">
  <h1>配置</h1>
  <p>所有设置放在 <code>~/.hermes/</code>，方便查看。</p>

  <h2>目录结构</h2>
  <pre><code>~/.hermes/
├── config.yaml     # 设置（model、terminal、TTS、压缩等）
├── .env            # API key 与 secret
├── auth.json       # OAuth 凭证（Nous Portal 等）
├── SOUL.md         # Agent 主身份（system prompt 第一槽）
├── memories/       # 持久化记忆（MEMORY.md / USER.md）
├── skills/         # Agent 管理的 skill（通过 skill_manage 工具）
├── cron/           # 定时任务
├── sessions/       # Gateway 会话
└── logs/           # 日志（errors.log / gateway.log，secret 自动脱敏）</code></pre>

  <h2>管理命令</h2>
  <pre><code>hermes config              # 查看当前配置
hermes config edit         # 在编辑器里打开 config.yaml
hermes config set KEY VAL  # 设置某个值
hermes config check        # 检查版本升级后是否缺失新选项
hermes config migrate      # 交互式补全

# 示例：
hermes config set model anthropic/claude-opus-4
hermes config set terminal.backend docker
hermes config set OPENROUTER_API_KEY sk-or-...  # 自动存到 .env</code></pre>
  <div class="hd-tip"><code>hermes config set</code> 会自动把值写到正确的文件：API key 去 <code>.env</code>，其他去 <code>config.yaml</code>。</div>

  <h2>优先级</h2>
  <p>从高到低：</p>
  <ol>
    <li><strong>CLI 参数</strong> —— 如 <code>hermes chat --model …</code>（单次覆盖）</li>
    <li><strong><code>~/.hermes/config.yaml</code></strong> —— 非 secret 的主配置</li>
    <li><strong><code>~/.hermes/.env</code></strong> —— env vars 的 fallback；Secret 必须放这里</li>
    <li><strong>内置默认值</strong></li>
  </ol>
  <div class="hd-info"><strong>经验法则</strong>：secret（API key、bot token、密码）放 <code>.env</code>，其他都放 <code>config.yaml</code>。</div>

  <h2>环境变量替换</h2>
  <p>可在 <code>config.yaml</code> 中用 <code>${VAR_NAME}</code> 引用环境变量：</p>
  <pre><code>auxiliary:
  vision:
    api_key: ${GOOGLE_API_KEY}
    base_url: ${CUSTOM_VISION_URL}

delegation:
  api_key: ${DELEGATION_KEY}</code></pre>
  <p>同一值里可以多次引用：<code>url: "${HOST}:${PORT}"</code>。引用未设置的变量会原样保留占位符。只支持 <code>${VAR}</code>，不识别裸 <code>$VAR</code>。</p>

  <h2>Provider 超时</h2>
  <p>可分别设置 provider 级与 model 级：</p>
  <pre><code>providers:
  openrouter:
    request_timeout_seconds: 120
    stale_timeout_seconds: 300
    models:
      anthropic/claude-opus-4:
        timeout_seconds: 180
        stale_timeout_seconds: 600</code></pre>
  <p>配置文件的值会覆盖历史环境变量 <code>HERMES_API_TIMEOUT</code> / <code>HERMES_API_CALL_STALE_TIMEOUT</code>。</p>

  <h2>关键配置节速览</h2>
  <ul>
    <li><code>model:</code> —— 默认模型、provider、base_url</li>
    <li><code>terminal:</code> —— 后端（local/docker/ssh/singularity/modal/daytona）、资源、持久化</li>
    <li><code>approvals:</code> —— 危险命令审批（见 <a data-page="user-guide/security">Security</a>）</li>
    <li><code>compression:</code> —— 上下文压缩阈值、摘要模型</li>
    <li><code>memory:</code> —— 字符上限、外部 memory provider</li>
    <li><code>display:</code> —— tool_progress、后台进程通知</li>
    <li><code>toolsets:</code> —— 每个平台启用哪些工具集</li>
    <li><code>mcp_servers:</code> —— MCP 配置</li>
    <li><code>gateway:</code> —— 会话重置策略、allowlist</li>
    <li><code>auxiliary:</code> —— vision / compression / delegation 的辅助 LLM</li>
    <li><code>providers:</code> —— provider 级 / 模型级细调</li>
  </ul>
  <p>全部环境变量见 <a data-page="reference/environment-variables">环境变量</a>。Provider 细节见 <a data-page="integrations/providers">AI Providers</a>。</p>
</section>

<section data-page="user-guide/sessions">
  <h1>会话管理</h1>
  <p>每次 Hermes 启动会开一个会话。会话保存全部消息、工具调用、模型、provider、token 计数，存到 <code>~/.hermes/state.db</code>（带 FTS5 全文索引）。</p>

  <h2>核心命令</h2>
  <pre><code>hermes sessions list                # 列出会话
hermes sessions list --recent 20    # 最近 20 个
hermes sessions search "关键词"     # 全文检索
hermes sessions export &lt;id&gt;         # 导出为 JSON / ShareGPT
hermes sessions rename &lt;id&gt; "名字"
hermes sessions delete &lt;id&gt;
hermes sessions prune --older-than 30d</code></pre>

  <h2>恢复会话</h2>
  <pre><code>hermes --continue          # 最近一次
hermes -c "标题关键词"     # 匹配最近一个标题
hermes --resume &lt;id&gt;       # 按 ID</code></pre>

  <h2>跨会话检索</h2>
  <p><code>session_search</code> 工具允许 Agent 在对话中检索自己之前的会话（见 <a data-page="user-guide/features/tools">Tools</a>）。</p>
  <p>完整说明见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/sessions/" target="_blank" rel="noopener">Sessions</a>。</p>
</section>

<section data-page="user-guide/profiles">
  <h1>Profile（多实例）</h1>
  <p>Profile 让你在一台机器上跑多个互相隔离的 Hermes 实例，各自有独立的 <code>HERMES_HOME</code>、配置、skills、sessions、Gateway 服务名。</p>

  <h2>常用命令</h2>
  <pre><code>hermes profile list                 # 列出全部 profile
hermes profile create work          # 新建
hermes profile use work             # 切换默认
hermes profile remove old-profile   # 删除
hermes -p work chat                 # 单次用某个 profile（不切默认）</code></pre>

  <p>每个 profile 对应 <code>~/.hermes-&lt;name&gt;/</code>。Gateway 服务名也会自动加后缀，避免冲突。</p>
  <p>详情见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/profiles/" target="_blank" rel="noopener">Profiles</a> 与 <a data-page="reference/profile-commands">Profile 命令参考</a>。</p>
</section>

<section data-page="user-guide/git-worktrees">
  <h1>Git Worktree 并行</h1>
  <p>用 <code>hermes -w</code> 在一个隔离的 git worktree 里启动 Agent，让多个 Agent 同时改同一个仓库互不干扰。</p>
  <pre><code>hermes -w                         # 交互式，自动创建 worktree
hermes -w -q "Fix issue #123"     # 单次查询
hermes -w -b my-branch            # 指定分支</code></pre>
  <p>退出后 worktree 可以保留也可以清理。推荐搭配 <a data-page="user-guide/features/delegation">Delegation</a> 或 <a data-page="guides/github-pr-review-agent">GitHub PR Review Agent</a>。原文：<a href="https://hermes-agent.nousresearch.com/docs/user-guide/git-worktrees/" target="_blank" rel="noopener">Git Worktrees</a>。</p>
</section>

<section data-page="user-guide/docker">
  <h1>在 Docker 中运行</h1>
  <p>Hermes 仓库提供 <code>Dockerfile</code>，可直接打成镜像或走 Compose。</p>
  <pre><code>docker build -t hermes-agent .
docker run --rm -it \
  -v $HOME/.hermes:/root/.hermes \
  -v $HOME/projects:/workspace \
  hermes-agent</code></pre>
  <p>如果你只是想让 <strong>terminal 工具</strong>在 Docker 容器里跑（而 Hermes 本身跑在主机上），设 <code>terminal.backend: docker</code> 即可，见 <a data-page="user-guide/features/tools">Tools</a>。原文：<a href="https://hermes-agent.nousresearch.com/docs/user-guide/docker/" target="_blank" rel="noopener">Docker</a>。</p>
</section>

<section data-page="user-guide/security">
  <h1>安全</h1>
  <p>Hermes 采用纵深防御的安全模型。本页覆盖从命令审批到容器隔离到各消息平台授权的每一层边界。</p>

  <h2>总览</h2>
  <p>共七层：</p>
  <ol>
    <li><strong>用户授权</strong> —— 谁可以和 Agent 说话（allowlist、DM pairing）</li>
    <li><strong>危险命令审批</strong> —— 破坏性操作走 human-in-the-loop</li>
    <li><strong>容器隔离</strong> —— Docker / Singularity / Modal 的沙箱 + 加固配置</li>
    <li><strong>MCP 凭证过滤</strong> —— 给 MCP 子进程做环境变量隔离</li>
    <li><strong>Context 文件扫描</strong> —— 在项目文件里做 prompt 注入检测</li>
    <li><strong>跨会话隔离</strong> —— 会话之间互不可见；cron 存储路径防 path traversal</li>
    <li><strong>输入消毒</strong> —— terminal 后端的工作目录参数会校验白名单，防 shell 注入</li>
  </ol>

  <h2>危险命令审批</h2>
  <p>执行命令前，Hermes 会对照一份危险模式清单进行匹配，命中则必须用户显式批准。</p>

  <h3>三种模式</h3>
  <pre><code>approvals:
  mode: manual    # manual | smart | off
  timeout: 60     # 等待用户响应的秒数（默认 60）</code></pre>
  <table>
    <thead><tr><th>模式</th><th>行为</th></tr></thead>
    <tbody>
      <tr><td><strong>manual</strong>（默认）</td><td>命中危险命令就问用户</td></tr>
      <tr><td><strong>smart</strong></td><td>用 auxiliary LLM 评估风险：低风险（如 <code>python -c "print('hi')"</code>）自动放行，确实危险的自动拒绝，把握不住的升级到人工确认</td></tr>
      <tr><td><strong>off</strong></td><td>全部关闭（等价 <code>--yolo</code>，全部无需批准）</td></tr>
    </tbody>
  </table>
  <div class="hd-warn"><code>approvals.mode: off</code> 会关掉所有安全提示。只在可信环境（CI/CD、容器等）里用。</div>

  <h3>YOLO 模式</h3>
  <p>绕过当前会话所有危险命令审批的三种触发方式：</p>
  <ol>
    <li>CLI 参数：<code>hermes --yolo</code> / <code>hermes chat --yolo</code></li>
    <li>斜杠命令：会话里 <code>/yolo</code> 切换</li>
    <li>环境变量：<code>HERMES_YOLO_MODE=1</code></li>
  </ol>
  <p><code>/yolo</code> 是切换开关，每次用一次翻转状态。</p>

  <h2>容器隔离（Docker / Singularity / Modal / Daytona）</h2>
  <ul>
    <li>根文件系统只读（Docker）</li>
    <li>所有 Linux capability drop 掉</li>
    <li>禁止提权</li>
    <li>PID 限制（256 进程）</li>
    <li>完整 namespace 隔离</li>
    <li>持久化工作区通过 volume，不写根层</li>
  </ul>
  <p>Docker 可以通过 <code>terminal.docker_forward_env</code> 显式放通一批环境变量，但这些变量在容器内命令可见，应视为已向该会话暴露。</p>

  <h2>MCP 凭证过滤</h2>
  <p>MCP stdio 子进程启动时只会拿到一份白名单环境变量，你配置里显式 <code>env:</code> 指定的那部分，再加上必要的系统变量（<code>PATH</code>、<code>HOME</code> 等）。Hermes 自己的 secret（OpenRouter / Anthropic API key 等）<strong>不会</strong>泄露给 MCP server。</p>

  <h2>平台授权</h2>
  <p>消息 Gateway <strong>默认拒绝</strong>所有不在 allowlist 且未 DM 配对的用户。详见 <a data-page="user-guide/messaging/index">Messaging 总览</a> 的 Security 一节与 DM Pairing。</p>

  <pre><code># 常用 allowlist
TELEGRAM_ALLOWED_USERS=123456789
DISCORD_ALLOWED_USERS=123456789012345678
SIGNAL_ALLOWED_USERS=+1555...
EMAIL_ALLOWED_USERS=trusted@example.com
GATEWAY_ALLOWED_USERS=123,456   # 跨平台统一

# 显式放开全部（不建议带 terminal 权限的 bot 用）
GATEWAY_ALLOW_ALL_USERS=true</code></pre>

  <h2>Context 文件扫描</h2>
  <p>加载 <code>AGENTS.md</code> / <code>.hermes.md</code> / <code>CLAUDE.md</code> / <code>.cursorrules</code> / <code>SOUL.md</code> 时会做 prompt 注入检测。命中可疑模式会截断或拒绝注入，并在日志中报警。</p>

  <h2>日志与 secret 脱敏</h2>
  <p><code>~/.hermes/logs/</code> 下的日志对 API key、bot token 等做自动脱敏。</p>

  <h2>生产部署建议</h2>
  <ul>
    <li>terminal 后端用 <code>ssh</code>、<code>docker</code> 或 <code>modal</code>，Agent 碰不到自己的代码</li>
    <li>所有平台都用 allowlist 或 DM pairing</li>
    <li>保留 <code>approvals.mode: manual</code> 或 <code>smart</code>，避免 YOLO</li>
    <li>Secret 放 <code>.env</code>，不要硬编码到 <code>config.yaml</code></li>
    <li>启用 container_persistent，但注意 volume 里的数据同样需要备份 / 访问控制</li>
  </ul>
  <p>本页为英文原文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/security/" target="_blank" rel="noopener">Security</a> 的完整翻译。</p>
</section>

<section data-page="user-guide/checkpoints-and-rollback">
  <h1>检查点与回滚</h1>
  <p>Hermes 在做文件修改前，会自动为工作目录创建 checkpoint（本地快照），出问题时可以用 <code>/rollback</code> 回退。</p>
  <ul>
    <li><strong>创建时机</strong>：工具 <code>patch</code>、<code>terminal</code>（判定为会改文件的命令）执行前</li>
    <li><strong>存储位置</strong>：<code>~/.hermes/checkpoints/&lt;session&gt;/&lt;序号&gt;/</code></li>
    <li><strong>清理策略</strong>：按数量 / 时间做 LRU，可配置</li>
  </ul>

  <h2>命令</h2>
  <pre><code>/rollback            # 列出本会话的所有 checkpoint
/rollback 3          # 回到第 3 个（之前的状态）
/rollback last       # 回到最后一次修改前</code></pre>
  <p>回滚是<strong>文件系统级</strong>的：只恢复被跟踪的文件状态，不会回滚对话历史。如果只是想"撤回一轮对话"，请用 <code>/undo</code>。原文：<a href="https://hermes-agent.nousresearch.com/docs/user-guide/checkpoints-and-rollback/" target="_blank" rel="noopener">Checkpoints & Rollback</a>。</p>
</section>

<!-- =========================================================================
  Features
  ========================================================================= -->

<section data-page="user-guide/features/overview">
  <h1>功能总览</h1>
  <p>Hermes Agent 包含一套远超普通"聊天"的能力：从持久化记忆、文件感知上下文，到浏览器自动化和语音对话，这些特性协同工作，让 Hermes 成为一个强大的自治助手。</p>

  <h2>核心</h2>
  <ul>
    <li><strong><a data-page="user-guide/features/tools">Tools & Toolsets</a></strong> —— Tool 是扩展 Agent 能力的函数，按逻辑归类到 toolset，可按平台启用/禁用。覆盖 web 搜索、终端执行、文件编辑、memory、委派等。</li>
    <li><strong><a data-page="user-guide/features/skills">Skills 系统</a></strong> —— Agent 需要时按需加载的知识文档。采用 progressive disclosure 最小化 token 占用，兼容 <a href="https://agentskills.io/specification" target="_blank" rel="noopener">agentskills.io</a> 开放标准。</li>
    <li><strong><a data-page="user-guide/features/memory">持久化 Memory</a></strong> —— 有边界、受管理的跨会话记忆。通过 <code>MEMORY.md</code> 与 <code>USER.md</code> 记住你的偏好、项目、环境、以及学到的东西。</li>
    <li><strong><a data-page="user-guide/features/context-files">Context Files</a></strong> —— 自动发现并加载项目上下文（<code>.hermes.md</code>、<code>AGENTS.md</code>、<code>CLAUDE.md</code>、<code>SOUL.md</code>、<code>.cursorrules</code>）。</li>
    <li><strong><a data-page="user-guide/features/context-references">@ 引用</a></strong> —— 输入 <code>@</code> 即可把文件、文件夹、git diff、URL 直接塞到消息里。</li>
    <li><strong><a data-page="user-guide/checkpoints-and-rollback">Checkpoints</a></strong> —— 修改前自动快照，<code>/rollback</code> 回退。</li>
  </ul>

  <h2>自动化</h2>
  <ul>
    <li><strong><a data-page="user-guide/features/cron">Cron</a></strong> —— 自然语言或 cron 表达式调度，支持挂 skill、投递到任何平台、暂停/恢复/编辑。</li>
    <li><strong><a data-page="user-guide/features/delegation">子代理委派</a></strong> —— <code>delegate_task</code> 派发隔离上下文、受限工具集的子 Agent；默认并发 3（可配置）。</li>
    <li><strong><a data-page="user-guide/features/code-execution">Code Execution</a></strong> —— <code>execute_code</code> 让 Agent 写 Python 脚本以 RPC 方式调 Hermes 自己的工具，把多步压缩成一次推理。</li>
    <li><strong><a data-page="user-guide/features/hooks">事件 Hook</a></strong> —— 在生命周期关键点跑自定义代码：日志、告警、webhook、工具拦截、指标、防护。</li>
    <li><strong><a data-page="user-guide/features/batch-processing">Batch</a></strong> —— 并行跑成百上千 prompt，输出 ShareGPT 格式 trajectory，用于训练数据生成或评测。</li>
  </ul>

  <h2>媒体与网页</h2>
  <ul>
    <li><strong><a data-page="user-guide/features/voice-mode">Voice Mode</a></strong> —— CLI 与消息平台的完整语音交互，Discord 语音频道实时对话。</li>
    <li><strong><a data-page="user-guide/features/browser">Browser Automation</a></strong> —— 多后端浏览器自动化：Browserbase 云、Browser Use 云、本地 Chrome via CDP、本地 Chromium。</li>
    <li><strong><a data-page="user-guide/features/vision">视觉与图像粘贴</a></strong> —— 多模态：CLI 里 <code>Ctrl+V</code> 粘贴图片给 Agent 分析。</li>
    <li><strong><a data-page="user-guide/features/image-generation">图像生成</a></strong> —— 通过 FAL.ai 生成图片，支持 FLUX 2 Klein/Pro、GPT-Image 1.5、Nano Banana Pro、Ideogram V3、Recraft V4 Pro、Qwen、Z-Image Turbo 等模型。</li>
    <li><strong><a data-page="user-guide/features/tts">TTS</a></strong> —— 五种 TTS provider：Edge TTS（免费）、ElevenLabs、OpenAI TTS、MiniMax、NeuTTS。</li>
  </ul>

  <h2>集成</h2>
  <ul>
    <li><strong><a data-page="user-guide/features/mcp">MCP</a></strong> —— 通过 stdio/HTTP 连接任意 MCP Server，支持按服务器过滤工具与 sampling。</li>
    <li><strong><a data-page="user-guide/features/provider-routing">Provider 路由</a></strong> —— 按 cost / speed / quality 精细控制，支持 sort / 白名单 / 黑名单 / 优先级。</li>
    <li><strong><a data-page="user-guide/features/fallback-providers">Fallback Provider</a></strong> —— 主模型出错时自动切备份，辅助任务（视觉、压缩）也有独立 fallback。</li>
    <li><strong><a data-page="user-guide/features/credential-pools">Credential Pools</a></strong> —— 同一 provider 多把 key 自动轮转。</li>
    <li><strong><a data-page="user-guide/features/memory-providers">Memory Providers</a></strong> —— Honcho、OpenViking、Mem0、Hindsight、Holographic、RetainDB、ByteRover。</li>
    <li><strong><a data-page="user-guide/features/api-server">API Server</a></strong> —— 暴露为 OpenAI 兼容 HTTP endpoint，可接 Open WebUI、LobeChat、LibreChat 等。</li>
    <li><strong><a data-page="user-guide/features/acp">ACP 编辑器集成</a></strong> —— VS Code / Zed / JetBrains。</li>
    <li><strong><a data-page="user-guide/features/rl-training">RL 训练</a></strong> —— 从会话生成 trajectory。</li>
  </ul>

  <h2>个性化</h2>
  <ul>
    <li><strong><a data-page="user-guide/features/personality">Personality 与 SOUL.md</a></strong> —— SOUL.md 是 system prompt 第一槽，定义 Agent 身份；还可用 <code>/personality</code> 临时切换内置 / 自定义人格。</li>
    <li><strong><a data-page="user-guide/features/skins">Skins 与主题</a></strong> —— CLI 视觉定制：横幅色、spinner、响应框标签、品牌文案、工具活动前缀。</li>
    <li><strong><a data-page="user-guide/features/plugins">Plugins</a></strong> —— 三类插件（通用 / Memory Provider / Context Engine），统一的 <code>hermes plugins</code> 交互 UI 管理。</li>
  </ul>
</section>

<section data-page="user-guide/features/tool-gateway">
  <h1>Nous Tool Gateway</h1>
  <p>付费的 <a href="https://portal.nousresearch.com" target="_blank" rel="noopener">Nous Portal</a> 订阅可以通过 <strong>Tool Gateway</strong> 使用 web 搜索、图像生成、TTS、浏览器自动化，无需单独申请各家 API key。</p>

  <h2>启用</h2>
  <pre><code>hermes model    # 选择 Nous Portal 作为 provider，并允许 Tool Gateway
# 或单独配置每个工具
hermes tools</code></pre>

  <h2>好处</h2>
  <ul>
    <li>单一账单，无需 Serper / Tavily / ElevenLabs / FAL / Browserbase 的独立额度</li>
    <li>默认超时、配额、限流已对 Hermes 调优</li>
    <li>跨工具复用同一份认证</li>
  </ul>
  <p>详见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/tool-gateway/" target="_blank" rel="noopener">Tool Gateway</a>。</p>
</section>

<section data-page="user-guide/features/tools">
  <h1>Tools & Toolsets</h1>
  <p>Tool 是扩展 Agent 能力的函数，按逻辑分组到 <strong>toolset</strong>，可按平台启用或禁用。</p>

  <h2>可用工具</h2>
  <p>Hermes 自带一份范围广泛的内置 tool registry，覆盖 web 搜索、浏览器自动化、终端执行、文件编辑、memory、委派、RL 训练、消息投递、Home Assistant 等。</p>
  <div class="hd-note"><strong>Honcho 跨会话记忆</strong> 是作为 memory provider 插件（<code>plugins/memory/honcho/</code>）提供的，不是内置 toolset。安装见 <a data-page="user-guide/features/plugins">Plugins</a>。</div>

  <p>高层分类：</p>
  <table>
    <thead><tr><th>类别</th><th>示例</th><th>说明</th></tr></thead>
    <tbody>
      <tr><td><strong>Web</strong></td><td><code>web_search</code>、<code>web_extract</code></td><td>搜索与网页正文抽取</td></tr>
      <tr><td><strong>终端与文件</strong></td><td><code>terminal</code>、<code>process</code>、<code>read_file</code>、<code>patch</code></td><td>执行命令与文件操作</td></tr>
      <tr><td><strong>浏览器</strong></td><td><code>browser_navigate</code>、<code>browser_snapshot</code>、<code>browser_vision</code></td><td>文本 + 视觉的交互式浏览器自动化</td></tr>
      <tr><td><strong>媒体</strong></td><td><code>vision_analyze</code>、<code>image_generate</code>、<code>text_to_speech</code></td><td>多模态分析与生成</td></tr>
      <tr><td><strong>Agent 编排</strong></td><td><code>todo</code>、<code>clarify</code>、<code>execute_code</code>、<code>delegate_task</code></td><td>规划、澄清、代码执行、子代理委派</td></tr>
      <tr><td><strong>记忆与检索</strong></td><td><code>memory</code>、<code>session_search</code></td><td>持久化记忆、跨会话检索</td></tr>
      <tr><td><strong>自动化与投递</strong></td><td><code>cronjob</code>、<code>send_message</code></td><td>定时任务（create/list/update/pause/resume/run/remove）、消息投递</td></tr>
      <tr><td><strong>集成</strong></td><td><code>ha_*</code>、MCP server tools、<code>rl_*</code></td><td>Home Assistant、MCP、RL 训练等</td></tr>
    </tbody>
  </table>
  <p>代码级真实清单见 <a data-page="reference/tools-reference">工具参考</a> 与 <a data-page="reference/toolsets-reference">工具集参考</a>。</p>

  <div class="hd-tip"><strong>Nous Tool Gateway</strong>：付费 Nous Portal 订阅可通过 <a data-page="user-guide/features/tool-gateway">Tool Gateway</a> 使用 web 搜索、图像生成、TTS、浏览器自动化，无需各自申请 API key。</div>

  <h2>使用 toolset</h2>
  <pre><code># 指定工具集启动
hermes chat --toolsets "web,terminal"

# 列出全部工具
hermes tools

# 按平台交互式配置
hermes tools</code></pre>
  <p>常见 toolset：<code>web</code>、<code>terminal</code>、<code>file</code>、<code>browser</code>、<code>vision</code>、<code>image_gen</code>、<code>moa</code>、<code>skills</code>、<code>tts</code>、<code>todo</code>、<code>memory</code>、<code>session_search</code>、<code>cronjob</code>、<code>code_execution</code>、<code>delegation</code>、<code>clarify</code>、<code>homeassistant</code>、<code>rl</code>。平台预设如 <code>hermes-cli</code>、<code>hermes-telegram</code>；MCP 动态集为 <code>mcp-&lt;server&gt;</code>。</p>

  <h2>Terminal 后端</h2>
  <table>
    <thead><tr><th>后端</th><th>说明</th><th>适用场景</th></tr></thead>
    <tbody>
      <tr><td><code>local</code></td><td>跑在你本机（默认）</td><td>开发、可信任务</td></tr>
      <tr><td><code>docker</code></td><td>隔离容器</td><td>安全、可复现</td></tr>
      <tr><td><code>ssh</code></td><td>远端服务器</td><td>沙箱，Agent 碰不到自己的代码</td></tr>
      <tr><td><code>singularity</code></td><td>HPC 容器</td><td>集群计算，rootless</td></tr>
      <tr><td><code>modal</code></td><td>云上执行</td><td>Serverless、按需扩展</td></tr>
      <tr><td><code>daytona</code></td><td>云沙箱工作区</td><td>持久化的远程开发环境</td></tr>
    </tbody>
  </table>

  <h3>配置</h3>
  <pre><code># ~/.hermes/config.yaml
terminal:
  backend: local    # 或 docker / ssh / singularity / modal / daytona
  cwd: "."
  timeout: 180</code></pre>

  <h3>Docker 后端</h3>
  <pre><code>terminal:
  backend: docker
  docker_image: python:3.11-slim</code></pre>

  <h3>SSH 后端（安全推荐）</h3>
  <pre><code>terminal:
  backend: ssh</code></pre>
  <pre><code># ~/.hermes/.env
TERMINAL_SSH_HOST=my-server.example.com
TERMINAL_SSH_USER=myuser
TERMINAL_SSH_KEY=~/.ssh/id_rsa</code></pre>

  <h3>Singularity / Apptainer</h3>
  <pre><code>apptainer build ~/python.sif docker://python:3.11-slim

hermes config set terminal.backend singularity
hermes config set terminal.singularity_image ~/python.sif</code></pre>

  <h3>Modal（Serverless 云）</h3>
  <pre><code>uv pip install modal
modal setup
hermes config set terminal.backend modal</code></pre>

  <h3>容器资源</h3>
  <pre><code>terminal:
  backend: docker  # 或 singularity / modal / daytona
  container_cpu: 1              # CPU 核（默认 1）
  container_memory: 5120        # MB（默认 5GB）
  container_disk: 51200         # MB（默认 50GB）
  container_persistent: true    # 跨会话持久化（默认 true）</code></pre>
  <p>当 <code>container_persistent: true</code> 时，已安装的包、文件、配置会跨会话保留。</p>

  <h3>容器安全</h3>
  <p>所有容器后端均以加固设置运行：只读根文件系统（Docker）、drop 全部 Linux capability、禁止提权、PID 限制 256、完整 namespace 隔离、持久化工作区只通过 volume 而非可写根层。</p>
  <p>Docker 可以通过 <code>terminal.docker_forward_env</code> 显式放通一批环境变量，但这些变量在容器内命令中可见，应视为已向该会话暴露。</p>

  <h2>后台进程管理</h2>
  <pre><code>terminal(command="pytest -v tests/", background=true)
# 返回：{"session_id": "proc_abc123", "pid": 12345}

# 用 process 工具管理：
process(action="list")
process(action="poll", session_id="proc_abc123")
process(action="wait", session_id="proc_abc123")
process(action="log",  session_id="proc_abc123")
process(action="kill", session_id="proc_abc123")
process(action="write", session_id="proc_abc123", data="y")</code></pre>
  <p>PTY 模式（<code>pty=true</code>）可启用交互式 CLI 工具（如 Codex、Claude Code）。</p>

  <h2>Sudo 支持</h2>
  <p>命令需要 sudo 时会提示你输入密码（当前会话缓存），或在 <code>~/.hermes/.env</code> 设置 <code>SUDO_PASSWORD</code>。</p>
  <div class="hd-warn">在消息平台上，如果 sudo 失败，输出会提示把 <code>SUDO_PASSWORD</code> 加到 <code>~/.hermes/.env</code>。</div>
  <p>本页为英文原文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/tools/" target="_blank" rel="noopener">Tools & Toolsets</a> 的完整翻译。</p>
</section>

<section data-page="user-guide/features/skills">
  <h1>Skills 系统</h1>
  <p>Skill 是 Agent 需要时按需加载的"知识文档"。采用 <strong>progressive disclosure</strong>（渐进披露）模式最小化 token 占用，并兼容 <a href="https://agentskills.io/specification" target="_blank" rel="noopener">agentskills.io</a> 开放标准。</p>

  <p>所有 skill 放在 <strong><code>~/.hermes/skills/</code></strong> —— 这是主目录和唯一可信源。全新安装时内置 skill 会从仓库里拷过来。从 Hub 安装或 Agent 自己创建的 skill 也落在这里，Agent 可以修改或删除任何 skill。</p>

  <p>你也可以让 Hermes 扫描<strong>外部 skill 目录</strong>（与本地目录并列扫描），见下文的"外部 skill 目录"一节。</p>

  <p>另见：</p>
  <ul>
    <li><a data-page="reference/skills-catalog">内置 Skills 清单</a></li>
    <li><a data-page="reference/optional-skills-catalog">可选 Skills 清单</a></li>
  </ul>

  <h2>使用 Skill</h2>
  <p>每个已安装 skill 会自动变成一个斜杠命令：</p>
  <pre><code># CLI 或任何消息平台里：
/gif-search funny cats
/axolotl help me fine-tune Llama 3 on my dataset
/github-pr-workflow create a PR for the auth refactor
/plan design a rollout for migrating our auth provider

# 只写 skill 名会加载它并让 Agent 主动问你需要什么：
/excalidraw</code></pre>
  <p>内置 <code>plan</code> skill 是很好的例子：<code>/plan [request]</code> 会让 Hermes 必要时先看看上下文，然后写一份 markdown 实施计划，而不是直接执行，结果会保存在当前后端工作目录下的 <code>.hermes/plans/</code>。</p>

  <p>也可以通过自然对话用 skill：</p>
  <pre><code>hermes chat --toolsets skills -q "What skills do you have?"
hermes chat --toolsets skills -q "Show me the axolotl skill"</code></pre>

  <h2>Progressive Disclosure</h2>
  <p>Skill 使用 token 友好的加载模式：</p>
  <pre><code>Level 0: skills_list()           → [{name, description, category}, ...]   (~3k tokens)
Level 1: skill_view(name)        → 全文 + 元数据                          （大小不等）
Level 2: skill_view(name, path)  → 具体参考文件                            （大小不等）</code></pre>
  <p>Agent 只在真正需要时才加载 skill 全文。</p>

  <h2>SKILL.md 格式</h2>
  <pre><code>---
name: my-skill
description: Brief description of what this skill does
version: 1.0.0
platforms: [macos, linux]     # 可选 —— 限定操作系统
metadata:
  hermes:
    tags: [python, automation]
    category: devops
    fallback_for_toolsets: [web]    # 可选 —— 条件激活（见下）
    requires_toolsets: [terminal]   # 可选 —— 条件激活（见下）
    config:                          # 可选 —— 与 config.yaml 的整合
      - key: my.setting
        description: "What this controls"
        default: "value"
        prompt: "Prompt for setup"
---

# Skill Title

## When to Use
触发条件。

## How to Use
用法说明、示例、参考文件。</code></pre>

  <h3>条件激活</h3>
  <ul>
    <li><code>requires_toolsets</code> —— 只有这些 toolset 全部启用时才暴露 skill</li>
    <li><code>fallback_for_toolsets</code> —— 当这些 toolset 未启用时作为 fallback 暴露</li>
  </ul>

  <h2>管理</h2>
  <pre><code>hermes skills                       # 交互式入口
hermes skills list
hermes skills search kubernetes
hermes skills install openai/skills/k8s
hermes skills publish ./my-skill     # 发布到 Hub
hermes skills audit                  # 对比本地 vs Hub
hermes skills config &lt;name&gt;          # 初始化该 skill 的 config.yaml 节</code></pre>
  <p>会话内也可 <code>/skills</code>。Agent 可通过 <code>skill_manage</code> 工具自动创建 / 更新 skill。</p>

  <h2>外部 skill 目录</h2>
  <p>在 <code>config.yaml</code> 里：</p>
  <pre><code>skills:
  external_dirs:
    - ~/work/company-skills
    - /mnt/shared/team-skills</code></pre>
  <p>这些目录会和 <code>~/.hermes/skills/</code> 一起扫描。Agent 仍只会把新 skill 写到主目录。</p>

  <h2>Skill 自改进</h2>
  <p>Agent 在使用中发现 skill 过时或缺步骤时，会触发 <code>skill_manage</code> 直接修改 skill 文件；这让 skill 会"越用越好"。详见架构页的 learning loop 一节。</p>
  <p>本页为英文原文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/skills/" target="_blank" rel="noopener">Skills System</a> 的完整翻译。</p>
</section>

<section data-page="user-guide/features/memory">
  <h1>持久化 Memory</h1>
  <p>Hermes 有一套有边界、受 Agent 管理的记忆，跨会话持久化。这让它能记住你的偏好、项目、环境、以及学到的东西。</p>

  <h2>工作方式</h2>
  <p>Agent 的记忆由两个文件构成：</p>
  <table>
    <thead><tr><th>文件</th><th>用途</th><th>字符上限</th></tr></thead>
    <tbody>
      <tr><td><strong>MEMORY.md</strong></td><td>Agent 的"个人笔记" —— 环境事实、约定、学到的东西</td><td>2,200 字符（≈ 800 tokens）</td></tr>
      <tr><td><strong>USER.md</strong></td><td>用户画像 —— 你的偏好、沟通风格、期望</td><td>1,375 字符（≈ 500 tokens）</td></tr>
    </tbody>
  </table>
  <p>两者都存在 <code>~/.hermes/memories/</code>，会话开始时以冻结快照形式注入 system prompt。Agent 通过 <code>memory</code> 工具自行管理 —— 可以 add / replace / remove。</p>
  <div class="hd-info">字符上限让记忆保持聚焦。容量满时，Agent 会做合并或替换以腾出空间。</div>

  <h2>在 system prompt 里的样子</h2>
  <p>每个会话启动时会从磁盘读取记忆并渲染进 system prompt：</p>
  <pre><code>══════════════════════════════════════════════
MEMORY (your personal notes) [67% — 1,474/2,200 chars]
══════════════════════════════════════════════
User's project is a Rust web service at ~/code/myapi using Axum + SQLx
§
This machine runs Ubuntu 22.04, has Docker and Podman installed
§
User prefers concise responses, dislikes verbose explanations</code></pre>
  <p>格式：header（属于 MEMORY 还是 USER PROFILE）、当前占用百分比与字符数、条目用 <code>§</code> 分隔，每条可多行。</p>

  <p><strong>Frozen snapshot 模式</strong>：system prompt 的这块内容在会话开始时抓一次快照，中途不变 —— 这是刻意设计，为了保住 LLM 的 prefix cache（大幅降延迟与成本）。Agent 在会话中 add/remove 会立刻写盘，但要下次会话才会出现在 system prompt 里；工具响应始终反映实时状态。</p>

  <h2>Memory 工具动作</h2>
  <ul>
    <li><strong>add</strong> —— 新增一条</li>
    <li><strong>replace</strong> —— 通过 <code>old_text</code> 子串匹配替换一条</li>
    <li><strong>remove</strong> —— 通过 <code>old_text</code> 子串匹配删除一条</li>
  </ul>
  <p>没有 <code>read</code> 动作 —— 记忆已通过 system prompt 注入，Agent 一进来就看得到。</p>

  <h3>子串匹配</h3>
  <pre><code># 如果记忆里有 "User prefers dark mode in all editors"
memory(action="replace", target="memory",
       old_text="dark mode",
       content="User prefers light mode in VS Code, dark mode in terminal")</code></pre>
  <p>如果子串命中多条，会返回错误要求你提供更唯一的匹配。</p>

  <h2>两个 target</h2>
  <h3><code>memory</code> —— Agent 的个人笔记</h3>
  <p>适合记：</p>
  <ul>
    <li>环境事实（OS、工具、项目结构）</li>
    <li>项目约定与配置</li>
    <li>工具怪异点与规避办法</li>
    <li>成功的命令组合</li>
  </ul>

  <h3><code>user_profile</code> —— 用户画像</h3>
  <p>适合记：你的沟通偏好、专业背景、长期目标、禁忌话题等。用来让 Agent 的风格和深度匹配你。</p>

  <h2>相关工具</h2>
  <ul>
    <li><strong><code>session_search</code></strong>：FTS5 检索你过往所有会话，Agent 可主动拉取历史。</li>
    <li><strong>Memory Providers</strong>：外部记忆后端（Honcho / Mem0 / OpenViking / 等），详见 <a data-page="user-guide/features/memory-providers">Memory Providers</a>。</li>
  </ul>

  <h2>配置</h2>
  <pre><code>memory:
  memory_char_limit: 2200
  user_profile_char_limit: 1375
  auto_nudge: true               # 定期提醒 Agent 审视并整理记忆
  nudge_interval_turns: 20</code></pre>
  <p>本页为英文原文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/memory/" target="_blank" rel="noopener">Persistent Memory</a> 的完整翻译。</p>
</section>

<section data-page="user-guide/features/memory-providers">
  <h1>Memory Providers（外部记忆后端）</h1>
  <p>除了内置的 <code>MEMORY.md</code> / <code>USER.md</code>，Hermes 还可以外接"跨会话用户建模"的专业记忆后端。当前支持的 provider：</p>
  <ul>
    <li><strong>Honcho</strong>（辩证式用户建模；与 <a data-page="user-guide/features/honcho">Honcho 集成</a> 深度配合）</li>
    <li><strong>OpenViking</strong></li>
    <li><strong>Mem0</strong></li>
    <li><strong>Hindsight</strong></li>
    <li><strong>Holographic</strong></li>
    <li><strong>RetainDB</strong></li>
    <li><strong>ByteRover</strong></li>
  </ul>

  <h2>启用</h2>
  <pre><code>hermes memory                      # 交互式配置
hermes plugins install memory-honcho   # 需要先装插件
hermes config set memory.provider honcho</code></pre>
  <p>完整清单与每个 provider 的配置键见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/memory-providers/" target="_blank" rel="noopener">Memory Providers</a>。</p>
</section>

<section data-page="user-guide/features/context-files">
  <h1>Context Files</h1>
  <p>Hermes 会自动发现并加载影响自身行为的上下文文件。有些是项目本地、按工作目录发现的；<code>SOUL.md</code> 现在是 Hermes 实例级全局文件，只从 <code>HERMES_HOME</code> 读取。</p>

  <h2>支持的 context 文件</h2>
  <table>
    <thead><tr><th>文件</th><th>用途</th><th>发现范围</th></tr></thead>
    <tbody>
      <tr><td><strong>.hermes.md</strong> / <strong>HERMES.md</strong></td><td>项目指令（最高优先级）</td><td>向上走到 git 根</td></tr>
      <tr><td><strong>AGENTS.md</strong></td><td>项目指令、约定、架构</td><td>启动时 CWD + 进入子目录时渐进发现</td></tr>
      <tr><td><strong>CLAUDE.md</strong></td><td>Claude Code 的 context 文件（也能识别）</td><td>启动时 CWD + 渐进发现</td></tr>
      <tr><td><strong>SOUL.md</strong></td><td>该 Hermes 实例的全局人格与语气</td><td>只看 <code>HERMES_HOME/SOUL.md</code></td></tr>
      <tr><td><strong>.cursorrules</strong></td><td>Cursor IDE 代码约定</td><td>只看 CWD</td></tr>
      <tr><td><strong>.cursor/rules/*.mdc</strong></td><td>Cursor 规则模块</td><td>只看 CWD</td></tr>
    </tbody>
  </table>
  <div class="hd-info"><strong>优先级</strong>：每会话只加载一种项目上下文（首个匹配胜出）：<code>.hermes.md</code> → <code>AGENTS.md</code> → <code>CLAUDE.md</code> → <code>.cursorrules</code>。<strong>SOUL.md</strong> 是独立的 Agent 身份（第一槽），始终加载。</div>

  <h2>AGENTS.md</h2>
  <p>项目级首选上下文文件，告诉 Agent 项目结构、遵循的约定、特殊说明。</p>

  <h3>渐进式子目录发现</h3>
  <p>启动时只加载当前目录的 <code>AGENTS.md</code>。当 Agent 进入子目录操作（<code>read_file</code>、<code>terminal</code>、<code>search_files</code> 等）时，才把那些目录里的 <code>AGENTS.md</code> 注入到会话里。</p>
  <pre><code>my-project/
├── AGENTS.md              ← 启动时加载（system prompt）
├── frontend/
│   └── AGENTS.md          ← Agent 读 frontend/ 时被发现
├── backend/
│   └── AGENTS.md          ← Agent 读 backend/ 时被发现
└── shared/
    └── AGENTS.md          ← Agent 读 shared/ 时被发现</code></pre>
  <p>好处：</p>
  <ul>
    <li><strong>system prompt 不膨胀</strong> —— 子目录提示按需出现</li>
    <li><strong>保住 prompt cache</strong> —— system prompt 跨轮稳定</li>
  </ul>
  <p>每个子目录每会话最多被扫一次。发现过程也会向上走父目录，所以读 <code>backend/src/main.py</code> 时即使 <code>backend/src/</code> 没 context，也能发现 <code>backend/AGENTS.md</code>。</p>
  <div class="hd-info">子目录 context 文件同样走 prompt 注入安全扫描；恶意文件会被拦截。</div>

  <h3>AGENTS.md 示例</h3>
  <pre><code># Project Context

This is a Next.js 14 web application with a Python FastAPI backend.

## Conventions
- TypeScript strict mode
- Tabs for indentation
- Tests live next to source: foo.ts → foo.test.ts

## Commands
- `pnpm dev` — frontend dev
- `uv run fastapi dev` — backend dev
- `pnpm test`、`uv run pytest`

## Architecture
- frontend/ — Next.js 14 App Router
- backend/  — FastAPI + SQLAlchemy
- shared/   — TypeScript + Python 类型定义</code></pre>

  <h2>.hermes.md</h2>
  <p>优先级高于 <code>AGENTS.md</code>。适合仓库级最高优先指令，例如"这个 repo 所有新功能必须先写设计文档"。</p>

  <h2>SOUL.md</h2>
  <p>只从 <code>HERMES_HOME/SOUL.md</code> 加载，占 system prompt 第一槽。详见 <a data-page="user-guide/features/personality">Personality</a>。</p>

  <h2 id="security-prompt-injection-protection">Prompt 注入防护</h2>
  <p>加载时会做模式检测 —— 如"忽略之前的指令"、角色扮演劫持、隐蔽越狱提示等。命中会被截断或拒绝，并在日志告警。</p>
  <p>本页为英文原文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/context-files/" target="_blank" rel="noopener">Context Files</a> 的完整翻译。</p>
</section>

<section data-page="user-guide/features/context-references">
  <h1>上下文引用（@ 引用）</h1>
  <p>在任何消息里输入 <code>@</code>，可以直接把<strong>文件、文件夹、git diff、URL</strong>的内容塞进 prompt。Agent 会原地扩展引用，并把内容附加到消息尾部。</p>

  <h2>支持的引用类型</h2>
  <ul>
    <li><code>@path/to/file.py</code> —— 单个文件</li>
    <li><code>@path/to/dir/</code> —— 目录（文件树 + 小文件内容）</li>
    <li><code>@git-diff</code> —— 当前未 commit 的 diff</li>
    <li><code>@git-diff HEAD~3</code> —— 最近 3 个 commit 的 diff</li>
    <li><code>@https://example.com/page</code> —— 网页</li>
    <li><code>@issue:123</code> / <code>@pr:42</code>（仓库已配置 GitHub 集成时）</li>
  </ul>

  <h2>Tab 补全</h2>
  <p>CLI / TUI 里输入 <code>@</code> 后按 Tab 会出现文件/目录补全。</p>

  <h2>示例</h2>
  <pre><code>@src/api.py @tests/test_api.py 给 POST /users 补测试，覆盖 400 与重复邮箱的场景
@git-diff 解释这次改动背后的原因
@https://example.com/spec 按这份规范实现</code></pre>
  <p>详细参数与注意事项见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/context-references/" target="_blank" rel="noopener">Context References</a>。</p>
</section>

<section data-page="user-guide/features/personality">
  <h1>Personality 与 SOUL.md</h1>
  <p>Hermes 的人格完全可定制。<code>SOUL.md</code> 是<strong>主身份</strong>—— system prompt 的第一块，决定 Agent 是谁。</p>
  <ul>
    <li><code>SOUL.md</code> —— 落在 <code>HERMES_HOME</code> 的持久身份文件，作为 Agent 身份（system prompt 第 1 槽）</li>
    <li>内置或自定义的 <code>/personality</code> 预设 —— 会话级的 system prompt 叠加层</li>
  </ul>
  <p>想换一个完全不同的 persona？改 <code>SOUL.md</code> 就好。</p>

  <h2>SOUL.md 是怎么工作的</h2>
  <p>Hermes 会自动在这里种一份默认 <code>SOUL.md</code>：</p>
  <pre><code>~/.hermes/SOUL.md</code></pre>
  <p>更确切说，用的是当前实例的 <code>HERMES_HOME</code>，所以自定义 home 目录时是：</p>
  <pre><code>$HERMES_HOME/SOUL.md</code></pre>

  <h3>重要行为</h3>
  <ul>
    <li><strong>SOUL.md 是 Agent 的主身份</strong>，占 system prompt 第 1 槽，替代硬编码的默认身份</li>
    <li>如果还不存在，Hermes 会自动生成一份起手式</li>
    <li>已存在的用户 <code>SOUL.md</code> 永不被覆盖</li>
    <li>Hermes <strong>只从 <code>HERMES_HOME</code></strong> 读取 <code>SOUL.md</code></li>
    <li>Hermes <strong>不会</strong>在当前工作目录查找 <code>SOUL.md</code></li>
    <li>若 <code>SOUL.md</code> 存在但为空或无法加载，Hermes 回退到内置默认身份</li>
    <li>有内容时，经安全扫描和长度截断后原样注入</li>
    <li><code>SOUL.md</code> <strong>不会</strong>在 context files 区重复出现 —— 只作为身份出现一次</li>
  </ul>
  <p>所以 <code>SOUL.md</code> 是真正的"每用户 / 每实例"的身份，而不是叠加层。</p>

  <h2>为什么这么设计</h2>
  <p>让人格可预测：如果从启动目录读 <code>SOUL.md</code>，不同项目切换时人格会意外变化。从 <code>HERMES_HOME</code> 读，人格属于 Hermes 实例本身。</p>
  <p>对用户教学也更容易 —— "改 <code>~/.hermes/SOUL.md</code> 改默认人格"。</p>

  <h2>/personality 预设</h2>
  <pre><code>/personality                # 列内置 + 用户自定义
/personality pirate         # 切到 pirate
/personality                # 再按一次清空 overlay 回到 SOUL.md 默认</code></pre>
  <p>Personality 预设是 system prompt 的"薄叠加层"：只在本会话覆盖语气/风格，不改身份本身。</p>

  <h3>自定义 personality</h3>
  <p>把文件放到 <code>~/.hermes/personalities/&lt;name&gt;.md</code>：</p>
  <pre><code># my-style

回答时一律使用 bullet list，最多 5 条，不带废话。
遇到代码问题先问清楚运行时版本再给答案。</code></pre>
  <p>然后 <code>/personality my-style</code> 即可。</p>

  <p>完整 frontmatter 字段、API 端点示例见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/personality/" target="_blank" rel="noopener">Personality & SOUL.md</a>。</p>
</section>

<section data-page="user-guide/features/skins">
  <h1>Skins & 主题</h1>
  <p>CLI 的视觉呈现可定制：启动横幅配色、spinner 表情和动词、响应框标签、品牌文案、工具活动前缀。皮肤是 YAML 文件，放 <code>~/.hermes/skins/</code>。</p>
  <pre><code>hermes skins list
hermes skins use retro
hermes skins edit retro</code></pre>
  <p>详见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/skins/" target="_blank" rel="noopener">Skins & Themes</a>。</p>
</section>

<section data-page="user-guide/features/plugins">
  <h1>插件系统</h1>
  <p>用插件添加自定义工具、hook、集成，而不必改核心代码。三类插件：</p>
  <ul>
    <li><strong>通用插件</strong> —— 新增 tool 或 hook</li>
    <li><strong>Memory Provider</strong> —— 跨会话的记忆后端（见 <a data-page="user-guide/features/memory-providers">Memory Providers</a>）</li>
    <li><strong>Context Engine</strong> —— 替换默认上下文管理策略</li>
  </ul>
  <h2>管理</h2>
  <pre><code>hermes plugins              # 交互式 UI
hermes plugins list
hermes plugins install &lt;name|path|git-url&gt;
hermes plugins enable &lt;name&gt;
hermes plugins disable &lt;name&gt;
hermes plugins remove &lt;name&gt;</code></pre>
  <p>开发插件见 <a data-page="guides/build-a-hermes-plugin">构建 Hermes 插件</a>。</p>
</section>

<section data-page="user-guide/features/built-in-plugins">
  <h1>内置插件</h1>
  <p>Hermes 仓库随附若干内置插件（位于 <code>plugins/</code>），开箱即可 <code>hermes plugins enable</code>：</p>
  <ul>
    <li><code>memory/honcho</code> —— Honcho 跨会话用户建模</li>
    <li><code>memory/openviking</code>、<code>memory/mem0</code> 等其他记忆后端</li>
    <li>通用插件：日志脱敏、使用量报告、Slack 告警 hook 等</li>
    <li>Context Engine 替代实现</li>
  </ul>
  <p>完整清单与启用说明见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/built-in-plugins/" target="_blank" rel="noopener">Built-in Plugins</a>。</p>
</section>

<section data-page="user-guide/features/cron">
  <h1>Cron 定时任务</h1>
  <p>让 Hermes 在未来某个时间点或按周期跑一个 prompt，结果投递回任何接入的平台。支持自然语言调度（"每天早上 8 点"）或标准 cron 表达式。</p>

  <h2>在会话里创建</h2>
  <pre><code>创建一个 cron：每天早上 8 点帮我扫一遍 inbox 并把今日要事发到 Telegram</code></pre>
  <p>Agent 会调用 <code>cronjob</code> 工具完成创建，可挂载 skill、指定投递平台。</p>

  <h2>命令</h2>
  <pre><code>hermes cron list
hermes cron show &lt;id&gt;
hermes cron pause &lt;id&gt;
hermes cron resume &lt;id&gt;
hermes cron run &lt;id&gt;          # 立即跑一次
hermes cron remove &lt;id&gt;
hermes cron tick               # 手动触发一次调度器心跳（调试用）</code></pre>
  <p>定时器实际由 Gateway 进程每 60 秒 tick 一次（见 <a data-page="user-guide/messaging/index">Messaging Gateway</a> 架构图）。配置见 <a data-page="guides/automate-with-cron">用 Cron 自动化</a>、排错见 <a data-page="guides/cron-troubleshooting">Cron 排错</a>。</p>
</section>

<section data-page="user-guide/features/delegation">
  <h1>子代理委派（Delegation）</h1>
  <p><code>delegate_task</code> 工具会创建一个<strong>独立子 Agent</strong>：有自己的 session、上下文、terminal 会话，受限的工具集。默认并发 3（可配置）。</p>

  <h2>典型用法</h2>
  <ul>
    <li>同时跑 3 个分支重构，父 Agent 汇总</li>
    <li>让子 Agent 专攻 "只读调研"，主 Agent 做修改</li>
    <li>把"浏览大量文件"这种吃 token 的活移出主上下文</li>
  </ul>

  <h2>配置</h2>
  <pre><code>delegation:
  max_concurrent: 3
  default_toolsets: [web, file, terminal]
  api_key: ${DELEGATION_KEY}     # 子 Agent 可用独立 provider / key</code></pre>
  <p>模式与最佳实践见 <a data-page="guides/delegation-patterns">子代理委派模式</a>；原文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/delegation/" target="_blank" rel="noopener">Delegation</a>。</p>
</section>

<section data-page="user-guide/features/code-execution">
  <h1>代码执行 execute_code</h1>
  <p><code>execute_code</code> 让 Agent 写一段 Python 脚本，在沙箱里通过 RPC 调用 Hermes 自己的工具。把"多步工具调用"压成"单步代码"——一次推理一次执行，大幅省 token。</p>

  <h2>示例</h2>
  <pre><code>execute_code(code="""
urls = ["https://a.com", "https://b.com", "https://c.com"]
pages = [web_extract(url=u) for u in urls]
summary = [{"url": u, "len": len(p)} for u, p in zip(urls, pages)]
print(summary)
""")</code></pre>
  <p>沙箱限制：进程隔离、有 CPU/内存/时间上限、网络走工具代理（不是任意 socket）、不能读写 Hermes 自身代码。详见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/code-execution/" target="_blank" rel="noopener">Code Execution</a>。</p>
</section>

<section data-page="user-guide/features/hooks">
  <h1>事件 Hook</h1>
  <p>在 Agent 生命周期的关键点跑自定义代码：</p>
  <ul>
    <li><strong>Gateway hooks</strong> —— 入站消息、出站消息、会话重置、错误。用于日志、告警、webhook。</li>
    <li><strong>Plugin hooks</strong> —— 工具调用前/后、tool 参数改写、工具结果改写、token 计量、护栏。</li>
  </ul>

  <h2>注册</h2>
  <p>通过插件入口点注册。hook 是同步或异步函数，收到结构化事件对象。</p>
  <p>完整事件清单、示例插件见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/hooks/" target="_blank" rel="noopener">Hooks</a>。</p>
</section>

<section data-page="user-guide/features/batch-processing">
  <h1>批处理（Batch）</h1>
  <p><code>batch_runner.py</code> 让你用相同的 Agent 配置并行跑成百上千 prompt，输出 ShareGPT 格式的 trajectory，可用于：训练数据生成、评测、离线回归测试。</p>

  <pre><code>python batch_runner.py \
  --prompts prompts.jsonl \
  --output trajectories.jsonl \
  --concurrency 16 \
  --toolsets web,terminal</code></pre>

  <p>trajectory 格式与后续处理见 <a data-page="developer-guide/trajectory-format">Trajectory 格式</a>；用于 RL 见 <a data-page="user-guide/features/rl-training">RL Training</a>。</p>
</section>

<section data-page="user-guide/features/voice-mode">
  <h1>Voice Mode 语音模式</h1>
  <p>Hermes 在 CLI 和消息平台上都支持完整语音交互：用麦克风说话、听 TTS 回复、在 Discord 语音频道做实时对话。</p>
  <p>想看手把手的推荐配置与真实用法，去 <a data-page="guides/use-voice-mode-with-hermes">用好 Voice Mode</a>。</p>

  <h2>前置</h2>
  <ol>
    <li><strong>Hermes 已安装</strong> —— <code>pip install hermes-agent</code></li>
    <li><strong>已配置 LLM provider</strong> —— <code>hermes model</code> 或在 <code>~/.hermes/.env</code> 中填凭据</li>
    <li><strong>基础会话可用</strong> —— 先用 <code>hermes</code> 发一条消息确认文本对话正常</li>
  </ol>
  <div class="hd-tip"><code>~/.hermes/</code> 与默认 <code>config.yaml</code> 在第一次 <code>hermes</code> 时自动创建；只有 <code>.env</code> 需要你手建来放 API key。</div>

  <h2>能力总览</h2>
  <table>
    <thead><tr><th>能力</th><th>平台</th><th>说明</th></tr></thead>
    <tbody>
      <tr><td><strong>交互式语音</strong></td><td>CLI</td><td>按 <code>Ctrl+B</code> 录音，Agent 自动检测静音并回复</td></tr>
      <tr><td><strong>自动语音回复</strong></td><td>Telegram / Discord</td><td>Agent 在文本回复旁附上语音</td></tr>
      <tr><td><strong>语音频道</strong></td><td>Discord</td><td>Bot 加入 VC，听说话并语音回复</td></tr>
    </tbody>
  </table>

  <h2>依赖</h2>
  <pre><code># CLI 语音模式（麦克风 + 播放）
pip install "hermes-agent[voice]"

# Discord + Telegram（含 discord.py[voice]）
pip install "hermes-agent[messaging]"

# 高级 TTS（ElevenLabs）
pip install "hermes-agent[tts-premium]"

# 本地 TTS（NeuTTS，可选）
python -m pip install -U neutts[all]

# 一把梭
pip install "hermes-agent[all]"</code></pre>
  <table>
    <thead><tr><th>Extra</th><th>包</th><th>作用</th></tr></thead>
    <tbody>
      <tr><td><code>voice</code></td><td><code>sounddevice</code>、<code>numpy</code></td><td>CLI 语音模式</td></tr>
      <tr><td><code>messaging</code></td><td><code>discord.py[voice]</code>、<code>python-telegram-bot</code>、<code>aiohttp</code></td><td>Discord & Telegram bot</td></tr>
      <tr><td><code>tts-premium</code></td><td><code>elevenlabs</code></td><td>ElevenLabs TTS</td></tr>
    </tbody>
  </table>

  <h2>CLI 语音模式</h2>
  <pre><code>/voice on       # 打开语音
/voice off      # 关闭
/voice status   # 查看当前 TTS / STT 配置
# 录音：按 Ctrl+B</code></pre>
  <p>STT 默认用 <code>faster-whisper</code>（本地，免费）。</p>

  <h2>消息平台语音回复</h2>
  <p>在 Gateway 的会话里：</p>
  <pre><code>/voice tts      # 在文本回复后附带 TTS 音频
/voice off      # 关闭</code></pre>
  <p>用户也可以直接发语音消息，Hermes 自动转写后当普通消息处理。</p>

  <h2>Discord 语音频道</h2>
  <pre><code>/voice join     # Bot 加入你当前的 VC
/voice leave    # 退出</code></pre>
  <p>进 VC 后，Bot 持续听说话并语音回复，文本 channel 也会打印 transcript。</p>

  <h2>TTS provider 选择</h2>
  <p>在 <code>~/.hermes/config.yaml</code> 配置：</p>
  <pre><code>tts:
  provider: edge        # edge | elevenlabs | openai | minimax | neutts
  voice: en-US-GuyNeural</code></pre>
  <p>五个 provider 各自的特点与 API key 详见 <a data-page="user-guide/features/tts">TTS</a>。</p>

  <h2>配置汇总</h2>
  <pre><code>voice:
  stt_provider: faster-whisper   # 或 openai, deepgram
  stt_language: auto
  vad:
    silence_ms: 800              # 静音判定阈值
    min_speech_ms: 250</code></pre>
  <p>本页对照英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/voice-mode/" target="_blank" rel="noopener">Voice Mode</a> 翻译。更多 Discord 语音频道的细节见原文"Discord Voice Channels"一节。</p>
</section>

<section data-page="user-guide/features/browser">
  <h1>浏览器自动化</h1>
  <p>Hermes 支持完整的浏览器自动化，5 种后端：</p>
  <ul>
    <li><strong>Browserbase</strong>（云）</li>
    <li><strong>Browser Use</strong>（云）</li>
    <li>本地 Chrome via CDP（连你已打开的 Chrome）</li>
    <li>本地 Chromium（Playwright 自带）</li>
    <li>本地 Firefox（有限支持）</li>
  </ul>

  <h2>工具集</h2>
  <ul>
    <li><code>browser_navigate</code>、<code>browser_snapshot</code>、<code>browser_click</code>、<code>browser_type</code>、<code>browser_vision</code> 等</li>
    <li><code>browser_vision</code> 让 Agent 用视觉模型看整页截图，更适合"结构复杂"的网站</li>
  </ul>

  <h2>配置</h2>
  <pre><code>browser:
  backend: local-chromium   # browserbase | browseruse | cdp | local-chromium | firefox
  headless: true
  viewport: { width: 1280, height: 800 }
  downloads_dir: ~/.hermes/browser-downloads</code></pre>
  <p>完整说明 & 云 API key 设置见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/browser/" target="_blank" rel="noopener">Browser Automation</a>。</p>
</section>

<section data-page="user-guide/features/vision">
  <h1>视觉（图像输入）</h1>
  <p>任何支持视觉的模型都可以让 Agent 看图。两种方式：</p>
  <ul>
    <li>CLI 里 <code>Ctrl+V</code> 粘贴剪贴板图片</li>
    <li>消息平台直接发图</li>
  </ul>

  <h2><code>vision_analyze</code> 工具</h2>
  <pre><code>vision_analyze(
  path_or_url="/tmp/shot.png",
  prompt="提取所有按钮上的文字并按位置排序"
)</code></pre>
  <p>默认走主模型；也可以用 <code>auxiliary.vision</code> 指定一个更便宜的视觉模型（见 <a data-page="user-guide/configuration">配置</a>）。英文原文：<a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/vision/" target="_blank" rel="noopener">Vision</a>。</p>
</section>

<section data-page="user-guide/features/image-generation">
  <h1>图像生成</h1>
  <p>通过 FAL.ai 生成图片，8 款模型：FLUX 2 Klein、FLUX 2 Pro、GPT-Image 1.5、Nano Banana Pro、Ideogram V3、Recraft V4 Pro、Qwen、Z-Image Turbo。用 <code>hermes tools</code> 选一个。</p>
  <pre><code>image_generate(
  prompt="A cyberpunk hedgehog reading Kafka on a rainy rooftop",
  model="flux-2-pro",
  size="1024x1024"
)</code></pre>
  <p>也能通过 <a data-page="user-guide/features/tool-gateway">Nous Tool Gateway</a> 走统一计费。原文：<a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/image-generation/" target="_blank" rel="noopener">Image Generation</a>。</p>
</section>

<section data-page="user-guide/features/tts">
  <h1>TTS 语音合成</h1>
  <p>五种 TTS provider：</p>
  <table>
    <thead><tr><th>Provider</th><th>特点</th><th>API Key</th></tr></thead>
    <tbody>
      <tr><td><strong>Edge TTS</strong></td><td>免费、质量不错、语言多</td><td>不需要</td></tr>
      <tr><td><strong>ElevenLabs</strong></td><td>最自然的商业 TTS</td><td><code>ELEVENLABS_API_KEY</code></td></tr>
      <tr><td><strong>OpenAI TTS</strong></td><td>稳定、延迟低</td><td><code>OPENAI_API_KEY</code></td></tr>
      <tr><td><strong>MiniMax</strong></td><td>中文效果佳</td><td><code>MINIMAX_API_KEY</code></td></tr>
      <tr><td><strong>NeuTTS</strong></td><td>本地跑、免费</td><td>不需要</td></tr>
    </tbody>
  </table>
  <pre><code>tts:
  provider: elevenlabs
  voice: Rachel
  speed: 1.0</code></pre>
  <p>配合 <a data-page="user-guide/features/voice-mode">Voice Mode</a> 使用。原文：<a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/tts/" target="_blank" rel="noopener">TTS</a>。</p>
</section>

<section data-page="user-guide/features/web-dashboard">
  <h1>Web Dashboard</h1>
  <p>本地 Web 界面，用来管理配置、API key、会话、插件等。</p>
  <pre><code>hermes dashboard        # 启动并打开浏览器
hermes dashboard --port 8765</code></pre>
  <p>默认只监听 localhost。功能覆盖：查看 config.yaml、设置 API key、浏览 sessions、管理 plugins、查看日志、启停 Gateway。原文：<a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/web-dashboard/" target="_blank" rel="noopener">Web Dashboard</a>。</p>
</section>

<section data-page="user-guide/features/dashboard-plugins">
  <h1>Dashboard 插件</h1>
  <p>Dashboard 本身也可被插件扩展 —— 注册新的侧边栏条目、自定义页面、REST 端点。适合给团队加"使用量面板"、"审计日志"之类的定制视图。开发指引见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/dashboard-plugins/" target="_blank" rel="noopener">Dashboard Plugins</a>。</p>
</section>

<section data-page="user-guide/features/rl-training">
  <h1>RL 训练数据</h1>
  <p>Hermes 与 Nous Research 的 <strong>Atropos</strong> 强化学习栈集成。会话里 Agent 的每次动作 + 结果都可以记录成 trajectory，用于：</p>
  <ul>
    <li>离线分析 / 失败分类</li>
    <li>SFT（监督微调）数据</li>
    <li>RLHF / RLAIF（用 reward model 做偏好学习）</li>
    <li>Atropos 管道的输入</li>
  </ul>

  <h2>产生 trajectory 的三种方式</h2>
  <ol>
    <li>CLI / messaging 日常会话，启用 <code>rl</code> toolset</li>
    <li><code>batch_runner.py</code> 跑 prompt 集合</li>
    <li><code>tinker-atropos</code> 集成模式</li>
  </ol>
  <p>Trajectory 格式细节见 <a data-page="developer-guide/trajectory-format">Trajectory 格式</a>。原文：<a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/rl-training/" target="_blank" rel="noopener">RL Training</a>。</p>
</section>

<section data-page="user-guide/skills/godmode">
  <h1>Godmode Skill</h1>
  <p><code>godmode</code> 是一个 Hermes 内置的高权限 skill，让 Agent 在需要时能执行更激进的自动修复（例如编辑自身配置、重启 Gateway、批量 patch）。默认不启用，要显式 <code>/godmode</code> 激活，并会在激活期间要求更严格的命令审批。</p>
  <p>完整行为、参数、安全取舍见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/skills/godmode/" target="_blank" rel="noopener">Godmode Skill</a>。</p>
</section>

<section data-page="user-guide/skills/google-workspace">
  <h1>Google Workspace Skill</h1>
  <p>用 Hermes 操作 Gmail、Drive、Docs、Calendar、Sheets 的组合 skill。OAuth 一次授权，Agent 之后可以："帮我把过去 7 天带 invoice 关键词的邮件附件整理到 Drive 对应文件夹"这样的任务。</p>
  <pre><code>/google-workspace                # 初次授权
/google-workspace 过去一周开过哪些会，列 TOP 5 主题</code></pre>
  <p>完整权限范围、隐私说明见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/skills/google-workspace/" target="_blank" rel="noopener">Google Workspace Skill</a>。</p>
</section>

<!-- =========================================================================
  Messaging Platforms
  ========================================================================= -->

<section data-page="user-guide/messaging/index">
  <h1>Messaging Gateway 总览</h1>
  <p>从 Telegram、Discord、Slack、WhatsApp、Signal、SMS、Email、Home Assistant、Mattermost、Matrix、DingTalk、Feishu/Lark、WeCom、Weixin、BlueBubbles（iMessage）、QQ 或浏览器跟 Hermes 聊。Gateway 是一个常驻后台进程，一并连接你配置好的所有平台，负责会话、跑 cron、投递语音消息。</p>

  <p>完整语音能力（CLI 麦克风、消息平台语音回复、Discord 语音频道）见 <a data-page="user-guide/features/voice-mode">Voice Mode</a> 与 <a data-page="guides/use-voice-mode-with-hermes">用好 Voice Mode</a>。</p>

  <h2>平台能力对比</h2>
  <table>
    <thead><tr><th>平台</th><th>语音</th><th>图片</th><th>文件</th><th>Thread</th><th>表情</th><th>Typing</th><th>流式</th></tr></thead>
    <tbody>
      <tr><td>Telegram</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>—</td><td>✅</td><td>✅</td></tr>
      <tr><td>Discord</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td></tr>
      <tr><td>Slack</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td></tr>
      <tr><td>WhatsApp</td><td>—</td><td>✅</td><td>✅</td><td>—</td><td>—</td><td>✅</td><td>✅</td></tr>
      <tr><td>Signal</td><td>—</td><td>✅</td><td>✅</td><td>—</td><td>—</td><td>✅</td><td>✅</td></tr>
      <tr><td>SMS</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
      <tr><td>Email</td><td>—</td><td>✅</td><td>✅</td><td>✅</td><td>—</td><td>—</td><td>—</td></tr>
      <tr><td>Home Assistant</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
      <tr><td>Mattermost</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>—</td><td>✅</td><td>✅</td></tr>
      <tr><td>Matrix</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td></tr>
      <tr><td>DingTalk</td><td>—</td><td>✅</td><td>✅</td><td>—</td><td>✅</td><td>—</td><td>✅</td></tr>
      <tr><td>Feishu/Lark</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td></tr>
      <tr><td>WeCom</td><td>✅</td><td>✅</td><td>✅</td><td>—</td><td>—</td><td>✅</td><td>✅</td></tr>
      <tr><td>WeCom Callback</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
      <tr><td>Weixin</td><td>✅</td><td>✅</td><td>✅</td><td>—</td><td>—</td><td>✅</td><td>✅</td></tr>
      <tr><td>BlueBubbles</td><td>—</td><td>✅</td><td>✅</td><td>—</td><td>✅</td><td>✅</td><td>—</td></tr>
      <tr><td>QQ</td><td>✅</td><td>✅</td><td>✅</td><td>—</td><td>—</td><td>✅</td><td>—</td></tr>
    </tbody>
  </table>

  <h2>架构</h2>
  <p>每个平台适配器收消息 → 按聊天维度路由到会话存储 → 交给 <code>AIAgent</code> 处理。Gateway 同时跑 cron 调度器，每 60 秒 tick 一次执行到期任务。</p>

  <h2>一键配置</h2>
  <pre><code>hermes gateway setup        # 交互式配置各平台</code></pre>

  <h2>Gateway 命令</h2>
  <pre><code>hermes gateway                         # 前台运行
hermes gateway setup                   # 交互式配置
hermes gateway install                 # 安装为用户服务（Linux systemd / macOS launchd）
sudo hermes gateway install --system   # Linux 专用：开机系统服务
hermes gateway start / stop / status
hermes gateway status --system         # Linux 专用，显式查看系统服务</code></pre>

  <h2>会话内斜杠命令</h2>
  <table>
    <thead><tr><th>命令</th><th>作用</th></tr></thead>
    <tbody>
      <tr><td><code>/new</code> 或 <code>/reset</code></td><td>新开会话</td></tr>
      <tr><td><code>/model [provider:model]</code></td><td>查看/切换模型（支持 <code>provider:model</code>）</td></tr>
      <tr><td><code>/provider</code></td><td>列 provider 及认证状态</td></tr>
      <tr><td><code>/personality [name]</code></td><td>切人格</td></tr>
      <tr><td><code>/retry</code></td><td>重试最后一条</td></tr>
      <tr><td><code>/undo</code></td><td>回滚最后一轮对话</td></tr>
      <tr><td><code>/status</code></td><td>会话信息</td></tr>
      <tr><td><code>/stop</code></td><td>打断 Agent</td></tr>
      <tr><td><code>/approve</code> / <code>/deny</code></td><td>同意/拒绝危险命令</td></tr>
      <tr><td><code>/sethome</code></td><td>把当前聊天设为 home channel</td></tr>
      <tr><td><code>/compress</code></td><td>手动压缩上下文</td></tr>
      <tr><td><code>/title [name]</code> / <code>/resume [name]</code></td><td>会话标题 / 恢复</td></tr>
      <tr><td><code>/usage</code> / <code>/insights [days]</code></td><td>用量 / 分析</td></tr>
      <tr><td><code>/reasoning [level|show|hide]</code></td><td>推理力度 / 显示</td></tr>
      <tr><td><code>/voice [on|off|tts|join|leave|status]</code></td><td>语音回复 / Discord 语音频道</td></tr>
      <tr><td><code>/rollback [number]</code></td><td>检查点</td></tr>
      <tr><td><code>/background &lt;prompt&gt;</code></td><td>后台会话</td></tr>
      <tr><td><code>/reload-mcp</code></td><td>重载 MCP</td></tr>
      <tr><td><code>/update</code></td><td>在线升级</td></tr>
      <tr><td><code>/help</code></td><td>命令列表</td></tr>
      <tr><td><code>/&lt;skill-name&gt;</code></td><td>调用任何已装 skill</td></tr>
    </tbody>
  </table>

  <h2>会话管理</h2>
  <p>会话跨消息保持上下文直到被重置。重置策略可按平台覆盖：</p>
  <table>
    <thead><tr><th>策略</th><th>默认</th><th>说明</th></tr></thead>
    <tbody>
      <tr><td>Daily</td><td>04:00</td><td>每天固定钟点重置</td></tr>
      <tr><td>Idle</td><td>1440 分钟</td><td>空闲 N 分钟后重置</td></tr>
      <tr><td>Both</td><td>（组合）</td><td>任一先到先触发</td></tr>
    </tbody>
  </table>
  <pre><code>// ~/.hermes/gateway.json
{
  "reset_by_platform": {
    "telegram": { "mode": "idle", "idle_minutes": 240 },
    "discord":  { "mode": "idle", "idle_minutes": 60 }
  }
}</code></pre>

  <h2>安全</h2>
  <p><strong>Gateway 默认拒绝</strong>所有不在 allowlist 且未 DM 配对的用户。对于带 terminal 权限的 bot，这是唯一的安全默认。</p>
  <pre><code># 白名单（推荐）
TELEGRAM_ALLOWED_USERS=123456789,987654321
DISCORD_ALLOWED_USERS=123456789012345678
SIGNAL_ALLOWED_USERS=+155****4567
SMS_ALLOWED_USERS=+155****4567
EMAIL_ALLOWED_USERS=trusted@example.com
MATTERMOST_ALLOWED_USERS=3uo8dkh1p7g1mfk49ear5fzs5c
MATRIX_ALLOWED_USERS=@alice:matrix.org
DINGTALK_ALLOWED_USERS=user-id-1
FEISHU_ALLOWED_USERS=ou_xxxxxxxx
WECOM_ALLOWED_USERS=user-id-1
WECOM_CALLBACK_ALLOWED_USERS=user-id-1

# 或者跨平台统一
GATEWAY_ALLOWED_USERS=123456789,987654321

# 或者显式放开全部（有 terminal 权限的 bot 不建议）
GATEWAY_ALLOW_ALL_USERS=true</code></pre>

  <h3>DM Pairing</h3>
  <p>免于手工填 ID：陌生用户 DM bot 时会拿到一次性配对码：</p>
  <pre><code># 用户端：Pairing code: XKGH5N7P
hermes pairing approve telegram XKGH5N7P

hermes pairing list
hermes pairing revoke telegram 123456789</code></pre>
  <p>配对码 1 小时过期、有限流、用密码学随机生成。</p>

  <h2>打断 Agent</h2>
  <p>Agent 工作时随时发消息即可打断：</p>
  <ul>
    <li>正在跑的 terminal 命令立即被 kill（SIGTERM，1 秒后 SIGKILL）</li>
    <li>工具调用被取消（只保留当前这一次）</li>
    <li>打断期间多条消息会合并为一个 prompt</li>
    <li><code>/stop</code> 只打断不追加</li>
  </ul>

  <h2>工具进度通知</h2>
  <pre><code>display:
  tool_progress: all    # off | new | all | verbose
  tool_progress_command: false   # 设 true 在消息平台里启用 /verbose</code></pre>
  <p>启用后 bot 会在干活时发状态消息：</p>
  <pre><code>💻 `ls -la`...
🔍 web_search...
📄 web_extract...
🐍 execute_code...</code></pre>

  <h2>后台会话</h2>
  <pre><code>/background Check all servers in the cluster and report any that are down</code></pre>
  <p>Hermes 立即回执：</p>
  <pre><code>🔄 Background task started: "Check all servers in the cluster..."
   Task ID: bg_143022_a1b2c3</code></pre>
  <p>每个 <code>/background</code> 派发一个<strong>独立 Agent 实例</strong>异步运行：</p>
  <ul>
    <li><strong>会话隔离</strong> —— 自己的历史，不知道你当前聊天的内容，仅拿你给的 prompt</li>
    <li><strong>配置相同</strong> —— 继承 model / provider / toolset / reasoning / routing</li>
    <li><strong>不阻塞</strong> —— 主聊天照常用，还可以再开多个后台任务</li>
    <li><strong>结果投递</strong> —— 完成后回到<strong>同一个聊天或频道</strong>，前缀 "✅ Background task complete"；失败则 "❌ Background task failed"</li>
  </ul>

  <h3>后台进程通知</h3>
  <pre><code>display:
  background_process_notifications: all    # all | result | error | off</code></pre>
  <table>
    <thead><tr><th>模式</th><th>收到什么</th></tr></thead>
    <tbody>
      <tr><td><code>all</code></td><td>运行中输出 + 最终完成消息（默认）</td></tr>
      <tr><td><code>result</code></td><td>只发最终完成消息（不管退出码）</td></tr>
      <tr><td><code>error</code></td><td>只在退出码非 0 时发</td></tr>
      <tr><td><code>off</code></td><td>完全不发 watcher 消息</td></tr>
    </tbody>
  </table>
  <p>环境变量：<code>HERMES_BACKGROUND_NOTIFICATIONS=result</code>。</p>

  <h2>服务管理</h2>
  <h3>Linux（systemd）</h3>
  <pre><code>hermes gateway install               # 安装为用户服务
hermes gateway start
hermes gateway stop
hermes gateway status
journalctl --user -u hermes-gateway -f

# 注销后仍保留
sudo loginctl enable-linger $USER

# 或装成开机系统服务（仍以你的用户身份运行）
sudo hermes gateway install --system
sudo hermes gateway start   --system
sudo hermes gateway status  --system
journalctl -u hermes-gateway -f</code></pre>
  <p>笔记本 / 开发机用用户服务；VPS / 无头主机用系统服务。避免同时装两个单元，否则 start/stop/status 行为会有歧义。</p>
  <div class="hd-info"><strong>多实例</strong>：如果一台机器跑多套 Hermes（不同 <code>HERMES_HOME</code>），每套服务名不同。默认 <code>~/.hermes</code> 叫 <code>hermes-gateway</code>；其他实例叫 <code>hermes-gateway-&lt;hash&gt;</code>。<code>hermes gateway</code> 命令会根据当前 <code>HERMES_HOME</code> 自动选对服务。</div>

  <h3>macOS（launchd）</h3>
  <pre><code>hermes gateway install               # 安装 launchd agent
hermes gateway start
hermes gateway stop
hermes gateway status
tail -f ~/.hermes/logs/gateway.log</code></pre>
  <p>生成的 plist 在 <code>~/Library/LaunchAgents/ai.hermes.gateway.plist</code>，包含三个环境变量：<code>PATH</code>（安装时的完整 shell PATH，前置 venv <code>bin/</code> 与 <code>node_modules/.bin</code>）、<code>VIRTUAL_ENV</code>、<code>HERMES_HOME</code>。</p>
  <div class="hd-tip"><strong>PATH 变化后</strong>：launchd plist 是静态的，装了 gateway 之后再新装工具（例如 nvm 新装 Node.js、brew 装 ffmpeg）要<strong>重跑 <code>hermes gateway install</code></strong> 以刷新 PATH，Gateway 会自动检测到陈旧 plist 并 reload。</div>

  <h2>平台级 toolset</h2>
  <p>每个平台有自己的 toolset：<code>hermes-cli</code> / <code>hermes-telegram</code> / <code>hermes-discord</code> / <code>hermes-whatsapp</code> / <code>hermes-slack</code> / <code>hermes-signal</code> / <code>hermes-sms</code> / <code>hermes-email</code> / <code>hermes-homeassistant</code>（附 HA 专用 tool：<code>ha_list_entities</code>、<code>ha_get_state</code>、<code>ha_call_service</code>、<code>ha_list_services</code>）/ <code>hermes-mattermost</code> / <code>hermes-matrix</code> / <code>hermes-dingtalk</code> / <code>hermes-feishu</code> / <code>hermes-wecom</code> / <code>hermes-wecom-callback</code> / <code>hermes-weixin</code> / <code>hermes-bluebubbles</code> / <code>hermes-qqbot</code> / <code>hermes</code>（API Server，默认）/ <code>hermes-webhook</code>。</p>

  <h2>下一步</h2>
  <ul>
    <li><a data-page="user-guide/messaging/telegram">Telegram</a></li>
    <li><a data-page="user-guide/messaging/discord">Discord</a></li>
    <li><a data-page="user-guide/messaging/slack">Slack</a></li>
    <li><a data-page="user-guide/messaging/whatsapp">WhatsApp</a></li>
    <li><a data-page="user-guide/messaging/signal">Signal</a></li>
    <li><a data-page="user-guide/messaging/sms">SMS（Twilio）</a></li>
    <li><a data-page="user-guide/messaging/email">Email</a></li>
    <li><a data-page="user-guide/messaging/homeassistant">Home Assistant</a></li>
    <li><a data-page="user-guide/messaging/mattermost">Mattermost</a></li>
    <li><a data-page="user-guide/messaging/matrix">Matrix</a></li>
    <li><a data-page="user-guide/messaging/dingtalk">DingTalk</a></li>
    <li><a data-page="user-guide/messaging/feishu">Feishu / Lark</a></li>
    <li><a data-page="user-guide/messaging/wecom">WeCom</a></li>
    <li><a data-page="user-guide/messaging/wecom-callback">WeCom Callback</a></li>
    <li><a data-page="user-guide/messaging/weixin">Weixin</a></li>
    <li><a data-page="user-guide/messaging/bluebubbles">BlueBubbles</a></li>
    <li><a data-page="user-guide/messaging/qqbot">QQBot</a></li>
    <li><a data-page="user-guide/messaging/open-webui">Open WebUI / API Server</a></li>
    <li><a data-page="user-guide/messaging/webhooks">Webhooks</a></li>
  </ul>
  <p>本页为英文原文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/" target="_blank" rel="noopener">Messaging Gateway</a> 的完整翻译。</p>
</section>

<section data-page="user-guide/messaging/telegram">
  <h1>Telegram 配置</h1>
  <p>Telegram 是最容易上手的平台之一：用 <a href="https://t.me/BotFather" target="_blank" rel="noopener">@BotFather</a> 创建 bot 拿到 token，填到 Hermes 就行。</p>

  <h2>1. 建 bot</h2>
  <ol>
    <li>在 Telegram 里 DM <strong>@BotFather</strong></li>
    <li>发 <code>/newbot</code>，按指引取名</li>
    <li>复制 Token（形如 <code>123456:ABC...</code>）</li>
    <li>可选：<code>/setdescription</code>、<code>/setuserpic</code> 美化</li>
  </ol>

  <h2>2. 填到 Hermes</h2>
  <pre><code>hermes gateway setup    # 选 Telegram 走引导
# 或手动
hermes config set TELEGRAM_BOT_TOKEN 123456:ABC...</code></pre>

  <h2>3. 授权</h2>
  <p>至少选一种：</p>
  <pre><code># 白名单（推荐）
TELEGRAM_ALLOWED_USERS=123456789,987654321

# 或用 DM Pairing
# 陌生用户 DM bot → 拿到一次性码 → 你在终端 approve
hermes pairing approve telegram XKGH5N7P</code></pre>
  <p>找自己的 user id：在 Telegram 里 DM <a href="https://t.me/userinfobot" target="_blank" rel="noopener">@userinfobot</a>。</p>

  <h2>4. 启动 Gateway</h2>
  <pre><code>hermes gateway          # 前台
# 或安装成服务
hermes gateway install
hermes gateway start</code></pre>

  <h2>5. 使用</h2>
  <p>打开自己的 bot，发条消息就行。斜杠命令和 <a data-page="user-guide/messaging/index">Messaging 总览</a> 里列的完全一致。</p>

  <h2>语音 & 文件</h2>
  <ul>
    <li>发语音消息：Hermes 自动转写后当普通消息处理</li>
    <li><code>/voice tts</code>：让 Hermes 的文本回复附带 TTS 语音</li>
    <li>发图片、文件：Hermes 可读取（图片过视觉模型）</li>
  </ul>

  <h2>高级</h2>
  <ul>
    <li><strong>多聊天 home</strong>：<code>/sethome</code> 把当前聊天标为 bot 主动推送的默认频道（cron、告警等）</li>
    <li><strong>群组 / Topic</strong>：支持 supergroup 和 topic；<code>TELEGRAM_ALLOWED_CHATS</code> 可限定群</li>
    <li><strong>反向代理 / 自建 Bot API</strong>：<code>TELEGRAM_BOT_API_BASE_URL</code> 切到自建</li>
  </ul>

  <h2>排错</h2>
  <ul>
    <li>"bot 不回消息"：<code>hermes gateway status</code>；看 <code>~/.hermes/logs/gateway.log</code></li>
    <li>"被拒绝"：检查 allowlist 或做 DM Pairing</li>
    <li>语音失败：装 <code>.[voice]</code> / <code>.[tts-premium]</code>，看 <a data-page="user-guide/features/voice-mode">Voice Mode</a></li>
  </ul>
  <p>完整字段 & 环境变量见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/telegram/" target="_blank" rel="noopener">Telegram</a>。</p>
</section>

<section data-page="user-guide/messaging/discord">
  <h1>Discord 配置</h1>
  <p>功能最丰富的平台之一：支持 DM、服务器频道、thread、emoji reaction、typing、流式编辑消息，还能进语音频道。</p>
  <h2>建 bot</h2>
  <ol>
    <li>去 <a href="https://discord.com/developers/applications" target="_blank" rel="noopener">Discord Developer Portal</a> 新建 Application → Bot</li>
    <li>启用 Privileged Intents：Message Content、Server Members</li>
    <li>复制 Bot Token</li>
    <li>OAuth2 → URL Generator 勾选 <code>bot</code>、<code>applications.commands</code>、Send Messages、Read Message History、Attach Files、Use Voice Activity 等</li>
  </ol>
  <h2>配置 Hermes</h2>
  <pre><code>hermes config set DISCORD_BOT_TOKEN &lt;token&gt;
hermes config set DISCORD_ALLOWED_USERS 123456789012345678
hermes gateway start</code></pre>
  <p>语音频道用 <code>/voice join</code>（详见 <a data-page="user-guide/features/voice-mode">Voice Mode</a>）。完整选项：<a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/discord/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/slack">
  <h1>Slack 配置</h1>
  <p>支持 Socket Mode（推荐，无需公网）与 Events API。</p>
  <pre><code>hermes config set SLACK_BOT_TOKEN xoxb-...
hermes config set SLACK_APP_TOKEN xapp-...    # Socket Mode
hermes config set SLACK_SIGNING_SECRET ...    # Events API
hermes config set SLACK_ALLOWED_USERS UXXXXX</code></pre>
  <p>建立步骤（Create app → OAuth scopes → Install）、语音回复、thread 模式见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/slack/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/whatsapp">
  <h1>WhatsApp 配置</h1>
  <p>通过 <a href="https://github.com/tulir/whatsmeow" target="_blank" rel="noopener">whatsmeow</a> Node 桥接，扫码登录同步 WhatsApp Web 的协议。</p>
  <pre><code>hermes whatsapp         # 扫码配对
hermes config set WHATSAPP_ALLOWED_USERS 861234567890@s.whatsapp.net
hermes gateway start</code></pre>
  <p>WhatsApp <strong>不支持</strong>语音消息 TTS 回复，也不支持 thread。更多：<a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/whatsapp/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/signal">
  <h1>Signal 配置</h1>
  <p>通过 <code>signal-cli</code> 或 <code>signald</code> 桥接，需要一个专用手机号做为 bot。</p>
  <pre><code>hermes config set SIGNAL_PHONE_NUMBER +15551234567
hermes config set SIGNAL_ALLOWED_USERS +15557654321
hermes gateway start</code></pre>
  <p>详见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/signal/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/email">
  <h1>Email 配置</h1>
  <p>基于 IMAP（拉取）+ SMTP（发送），支持 Gmail、Fastmail、自建。每一封邮件（或一个邮件线程）对应一个会话。</p>
  <pre><code>EMAIL_IMAP_HOST=imap.gmail.com
EMAIL_IMAP_USER=me@example.com
EMAIL_IMAP_PASSWORD=app-password
EMAIL_SMTP_HOST=smtp.gmail.com
EMAIL_SMTP_USER=me@example.com
EMAIL_SMTP_PASSWORD=app-password
EMAIL_ALLOWED_USERS=trusted@example.com</code></pre>
  <p>附件支持、HTML / Markdown 回复、轮询间隔见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/email/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/sms">
  <h1>SMS（Twilio）</h1>
  <p>通过 Twilio Messaging API。只能文本，不支持附件。</p>
  <pre><code>TWILIO_ACCOUNT_SID=AC...
TWILIO_AUTH_TOKEN=...
TWILIO_FROM_NUMBER=+15551234567
SMS_ALLOWED_USERS=+15557654321</code></pre>
  <p>你需要公网可达的 webhook URL（用 ngrok 或真正部署）。详见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/sms/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/homeassistant">
  <h1>Home Assistant 集成</h1>
  <p>让 Hermes 通过 Home Assistant 控制家里设备、读取传感器、触发 automation。工具集 <code>hermes-homeassistant</code> 提供：<code>ha_list_entities</code>、<code>ha_get_state</code>、<code>ha_call_service</code>、<code>ha_list_services</code>。</p>
  <pre><code>HOMEASSISTANT_URL=https://homeassistant.local:8123
HOMEASSISTANT_TOKEN=&lt;long-lived-token&gt;</code></pre>
  <p>长期 token 在 HA 里 Profile → Security → Create Token 生成。详细示例："睡前帮我关掉客厅灯 + 调低空调"见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/homeassistant/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/mattermost">
  <h1>Mattermost 配置</h1>
  <p>自建 Slack 替代方案。</p>
  <pre><code>MATTERMOST_URL=https://mm.example.com
MATTERMOST_TOKEN=&lt;personal-access-token&gt;
MATTERMOST_TEAM=my-team
MATTERMOST_ALLOWED_USERS=3uo8dkh1p7g1mfk49ear5fzs5c</code></pre>
  <p>详见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/mattermost/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/matrix">
  <h1>Matrix 配置</h1>
  <p>开源去中心化 IM 协议。Hermes 作为 Matrix bot 连 homeserver（matrix.org、自建 Synapse、Dendrite 等）。</p>
  <pre><code>MATRIX_HOMESERVER=https://matrix.org
MATRIX_USER_ID=@mybot:matrix.org
MATRIX_ACCESS_TOKEN=...
MATRIX_ALLOWED_USERS=@alice:matrix.org</code></pre>
  <p>E2EE、房间白名单、DM vs 群组见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/matrix/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/dingtalk">
  <h1>钉钉 DingTalk 配置</h1>
  <p>企业内部 bot。需要在钉钉开放平台建应用，开启机器人，拿到 AppKey / AppSecret / Robot Code。</p>
  <pre><code>DINGTALK_APP_KEY=...
DINGTALK_APP_SECRET=...
DINGTALK_ROBOT_CODE=...
DINGTALK_ALLOWED_USERS=user-id-1,user-id-2</code></pre>
  <p>群会话 vs 单聊、消息类型见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/dingtalk/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/feishu">
  <h1>飞书 / Lark 配置</h1>
  <p>飞书开放平台自建应用 → 开启机器人、会话消息、文件上传、语音消息权限。Hermes 在飞书里支持语音 / 图片 / 文件 / thread / 表情回复 / typing / 流式。</p>
  <pre><code>FEISHU_APP_ID=cli_...
FEISHU_APP_SECRET=...
FEISHU_VERIFICATION_TOKEN=...
FEISHU_ENCRYPT_KEY=...
FEISHU_ALLOWED_USERS=ou_xxxxxxxx</code></pre>
  <p>Lark（海外版）用 <code>FEISHU_HOST=open.larksuite.com</code>。详见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/feishu/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/wecom">
  <h1>企业微信 WeCom 配置</h1>
  <p>企业微信自建应用模式。</p>
  <pre><code>WECOM_CORP_ID=...
WECOM_AGENT_ID=...
WECOM_SECRET=...
WECOM_ALLOWED_USERS=user-id-1,user-id-2</code></pre>
  <p>群会话、媒体文件、语音见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/wecom/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/wecom-callback">
  <h1>企业微信回调</h1>
  <p>企业微信的被动消息回调模式。用来接收企业微信客户联系助手之类发过来的事件。需要一个公网 URL 作为回调接口，并在企业微信后台配好 Token / EncodingAESKey。</p>
  <pre><code>WECOM_CALLBACK_TOKEN=...
WECOM_CALLBACK_AES_KEY=...
WECOM_CALLBACK_ALLOWED_USERS=user-id-1</code></pre>
  <p>详见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/wecom-callback/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/weixin">
  <h1>微信公众号 Weixin</h1>
  <p>微信公众号（服务号 / 订阅号）接入。用户在公众号里发消息，bot 回复。</p>
  <pre><code>WEIXIN_APP_ID=...
WEIXIN_APP_SECRET=...
WEIXIN_TOKEN=...
WEIXIN_AES_KEY=...</code></pre>
  <p>客服消息 / 模板消息两种发送模式见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/weixin/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/bluebubbles">
  <h1>BlueBubbles（iMessage）</h1>
  <p>在一台 Mac 上跑 <a href="https://bluebubbles.app/" target="_blank" rel="noopener">BlueBubbles Server</a>，Hermes 通过它的 HTTP API 与 iMessage 相互收发。</p>
  <pre><code>BLUEBUBBLES_URL=http://mac-mini.local:1234
BLUEBUBBLES_PASSWORD=...</code></pre>
  <p>局限：无语音回复、无 thread。详见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/bluebubbles/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/qqbot">
  <h1>QQ 机器人</h1>
  <p>通过 QQ 官方开放平台的 Bot API 接入。需要在 QQ 开放平台建机器人、过审、拿到 AppID / Secret。</p>
  <pre><code>QQ_APP_ID=...
QQ_APP_SECRET=...
QQ_TOKEN=...</code></pre>
  <p>群聊、私聊、媒体见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/qqbot/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/open-webui">
  <h1>Open WebUI / API Server</h1>
  <p>Hermes 可以暴露一个 OpenAI 兼容的 HTTP endpoint，让任何 OpenAI 兼容的前端当成"一个模型"使用：<a href="https://openwebui.com/" target="_blank" rel="noopener">Open WebUI</a>、LobeChat、LibreChat、甚至 JetBrains AI Assistant。</p>
  <pre><code>hermes mcp serve --port 8765          # 或
hermes api-server --host 0.0.0.0 --port 8765</code></pre>
  <p>Open WebUI 里加一个 Connection：Base URL 指到 <code>http://host:8765/v1</code>，随便填个 API key（Hermes 可用 <code>API_SERVER_TOKEN</code> 做鉴权）。详见 <a data-page="user-guide/features/api-server">API Server</a> 与 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/open-webui/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/messaging/webhooks">
  <h1>Webhooks</h1>
  <p>Hermes 可以订阅/接收 webhook：任何外部系统（GitHub、Linear、Zapier、监控系统）发 POST 过来，Hermes 当作一条"带上下文的消息"来处理，然后按你的规则回调或投递到某个平台。</p>
  <pre><code>hermes webhook list
hermes webhook create --name github --on-event pull_request --route-to telegram
hermes webhook rotate-secret github</code></pre>
  <p>签名验证（HMAC）、事件过滤、模板化回调见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/webhooks/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<!-- =========================================================================
  Integrations
  ========================================================================= -->

<section data-page="integrations/index">
  <h1>集成总览</h1>
  <p>Hermes 被设计为"能装进你现有工具链"。核心集成点：</p>
  <ul>
    <li><strong><a data-page="integrations/providers">AI Providers</a></strong> —— 模型供应商（Nous Portal、OpenRouter、OpenAI、Anthropic、Google、Ollama、vLLM、SGLang、llama.cpp、LiteLLM 等）</li>
    <li><strong><a data-page="user-guide/features/mcp">MCP</a></strong> —— 接入任意 MCP Server</li>
    <li><strong><a data-page="user-guide/features/acp">ACP</a></strong> —— 编辑器集成（VS Code、Zed、JetBrains）</li>
    <li><strong><a data-page="user-guide/features/api-server">API Server</a></strong> —— OpenAI 兼容的 HTTP endpoint</li>
    <li><strong><a data-page="user-guide/features/honcho">Honcho</a></strong> —— 跨会话用户建模</li>
    <li><strong><a data-page="user-guide/features/provider-routing">Provider 路由</a></strong>、<strong><a data-page="user-guide/features/fallback-providers">Fallback</a></strong>、<strong><a data-page="user-guide/features/credential-pools">Credential Pool</a></strong></li>
  </ul>
  <p>一些常用 combo：</p>
  <ul>
    <li>成本优化：OpenRouter + Provider 路由 + Credential Pool</li>
    <li>高可用：主 provider + Fallback + smart 审批</li>
    <li>私有部署：自建 vLLM + Honcho + MCP filesystem + API Server → Open WebUI</li>
  </ul>
</section>

<section data-page="integrations/providers">
  <h1>AI Providers（模型供应商）</h1>
  <p>Hermes 理论上支持任何 OpenAI 兼容端点。下表列出官方测试过的 provider：</p>
  <table>
    <thead><tr><th>Provider</th><th>环境变量</th><th>备注</th></tr></thead>
    <tbody>
      <tr><td><strong>Nous Portal</strong></td><td><code>NOUS_API_KEY</code>（OAuth 走 <code>hermes auth</code>）</td><td>官方推荐，带 Tool Gateway</td></tr>
      <tr><td><strong>OpenRouter</strong></td><td><code>OPENROUTER_API_KEY</code></td><td>一个 key 几百个模型</td></tr>
      <tr><td><strong>OpenAI</strong></td><td><code>OPENAI_API_KEY</code></td><td>GPT-4o / o1 / o3 等</td></tr>
      <tr><td><strong>Anthropic</strong></td><td><code>ANTHROPIC_API_KEY</code></td><td>Claude 系列（原生 API）</td></tr>
      <tr><td><strong>Google Gemini</strong></td><td><code>GOOGLE_API_KEY</code></td><td>Gemini 系列</td></tr>
      <tr><td><strong>z.ai / ZhipuAI</strong></td><td><code>ZHIPU_API_KEY</code></td><td>GLM 系列</td></tr>
      <tr><td><strong>Moonshot / Kimi</strong></td><td><code>MOONSHOT_API_KEY</code></td><td>Kimi 系列</td></tr>
      <tr><td><strong>MiniMax</strong></td><td><code>MINIMAX_API_KEY</code></td><td>全球 / 国内 endpoint</td></tr>
      <tr><td><strong>DeepSeek</strong></td><td><code>DEEPSEEK_API_KEY</code></td><td>DeepSeek V3 / R1</td></tr>
      <tr><td><strong>GitHub Copilot</strong></td><td><code>hermes auth copilot</code></td><td>OAuth</td></tr>
      <tr><td><strong>Azure OpenAI</strong></td><td><code>AZURE_OPENAI_*</code></td><td>企业部署</td></tr>
      <tr><td><strong>AWS Bedrock</strong></td><td>AWS 凭据链</td><td>见 <a data-page="guides/aws-bedrock">AWS Bedrock 指南</a></td></tr>
      <tr><td><strong>Ollama</strong></td><td>无需 key</td><td>本地</td></tr>
      <tr><td><strong>vLLM / SGLang / llama.cpp</strong></td><td>自设</td><td>任意 OpenAI 兼容 endpoint</td></tr>
      <tr><td><strong>LiteLLM Proxy</strong></td><td>自设</td><td>多 provider 代理</td></tr>
      <tr><td><strong>Custom endpoint</strong></td><td>你自己填</td><td>任意 OpenAI 兼容</td></tr>
    </tbody>
  </table>
  <p>完整的 <code>provider:</code> 节配置字段见英文原文 <a href="https://hermes-agent.nousresearch.com/docs/integrations/providers/" target="_blank" rel="noopener">AI Providers</a>。</p>
  <h2>选一个的快速建议</h2>
  <ul>
    <li>"就想跑起来，不折腾" → OpenRouter 或 Nous Portal</li>
    <li>"已经有 Claude / Codex 订阅" → 直接 OAuth</li>
    <li>"要跑本地模型" → Ollama + <code>ctx_size ≥ 65536</code></li>
    <li>"企业 / 合规" → Azure OpenAI 或 AWS Bedrock</li>
  </ul>
</section>

<section data-page="user-guide/features/mcp">
  <h1>MCP（Model Context Protocol）</h1>
  <p>MCP 让 Hermes 连接外部 tool server，用上那些"已经存在于别的地方"的工具 —— GitHub、数据库、文件系统、浏览器栈、内部 API 等。只要你想让 Hermes 用某个已有工具，MCP 通常是最干净的方式。</p>

  <h2>MCP 能给你什么</h2>
  <ul>
    <li>无需先写一个原生 Hermes tool，就能接入外部工具生态</li>
    <li>本地 stdio server + 远程 HTTP MCP server，同一份配置里混用</li>
    <li>启动时自动发现并注册工具</li>
    <li>对支持的 server 提供 MCP resource / prompt 的封装</li>
    <li>按服务器做工具过滤，只暴露你想让 Hermes 看到的那几个 MCP 工具</li>
  </ul>

  <h2>快速上手</h2>
  <ol>
    <li>安装 MCP 支持（标准 install 脚本已包含）：
<pre><code>cd ~/.hermes/hermes-agent
uv pip install -e ".[mcp]"</code></pre>
    </li>
    <li>在 <code>~/.hermes/config.yaml</code> 加一个 MCP server：
<pre><code>mcp_servers:
  filesystem:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-filesystem", "/home/user/projects"]</code></pre>
    </li>
    <li>启动 Hermes：<code>hermes chat</code></li>
    <li>直接让 Hermes 用它，例如："列出 /home/user/projects 下的文件并总结仓库结构"。</li>
  </ol>

  <h2>两类 MCP server</h2>
  <h3>stdio server</h3>
  <p>本地子进程，走 stdin/stdout：</p>
  <pre><code>mcp_servers:
  github:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-github"]
    env:
      GITHUB_PERSONAL_ACCESS_TOKEN: "***"</code></pre>
  <p>适合：本机安装、本地资源、按 MCP server 官方 README 用 <code>command</code>/<code>args</code>/<code>env</code> 的情况。</p>

  <h3>HTTP server</h3>
  <p>远程 endpoint，Hermes 直接连：</p>
  <pre><code>mcp_servers:
  remote:
    url: "https://mcp.example.com/mcp"
    headers:
      Authorization: "Bearer ${REMOTE_MCP_TOKEN}"</code></pre>

  <h2>按服务器过滤工具</h2>
  <pre><code>mcp_servers:
  github:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-github"]
    env:
      GITHUB_PERSONAL_ACCESS_TOKEN: "***"
    allow_tools:                  # 白名单（支持 glob）
      - "get_pull_request*"
      - "list_issues"
    deny_tools:                   # 黑名单
      - "delete_*"</code></pre>
  <p><code>allow_tools</code> 优先；若未设则默认全放，再按 <code>deny_tools</code> 排除。</p>

  <h2>会话内重载</h2>
  <pre><code>/reload-mcp</code></pre>

  <h2>把 Hermes 当 MCP server 用</h2>
  <pre><code>hermes mcp serve</code></pre>
  <p>让其他 MCP 客户端（Claude Desktop、Cursor）把 Hermes 当成一个"带工具链的 Agent 工具"调用。</p>

  <h2>配置参考</h2>
  <p>完整字段见 <a data-page="reference/mcp-config-reference">MCP 配置参考</a>。实战套路：<a data-page="guides/use-mcp-with-hermes">用好 MCP + Hermes</a>。本页为英文原文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/mcp/" target="_blank" rel="noopener">MCP</a> 的完整翻译。</p>
</section>

<section data-page="user-guide/features/acp">
  <h1>ACP（编辑器集成）</h1>
  <p><a href="https://agentcommunicationprotocol.dev" target="_blank" rel="noopener">Agent Communication Protocol</a> 让你在 ACP 兼容编辑器里直接用 Hermes —— VS Code（配 ACP 插件）、Zed（原生）、JetBrains。聊天、工具活动、文件 diff、终端命令都渲染在编辑器里。</p>
  <pre><code>pip install -e '.[acp]'
hermes acp                # 启动 ACP server
hermes acp --port 12345   # 自定义端口</code></pre>
  <p>在编辑器里把 Hermes 作为 ACP provider 添加即可。详细安装、协议细节见 <a data-page="developer-guide/acp-internals">ACP 内部</a> 与 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/acp/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/features/api-server">
  <h1>OpenAI 兼容 API Server</h1>
  <p>把 Hermes 作为 OpenAI 兼容 <code>/v1/chat/completions</code> endpoint 暴露，前端任意（Open WebUI、LobeChat、LibreChat、你自己的应用）都能以"就是一个强力模型"的方式调用它。</p>
  <pre><code>hermes api-server --host 0.0.0.0 --port 8765
# 鉴权
hermes config set API_SERVER_TOKEN &lt;token&gt;</code></pre>
  <p>请求示例：</p>
  <pre><code>curl http://localhost:8765/v1/chat/completions \
  -H "Authorization: Bearer $API_SERVER_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "hermes",
    "messages": [{"role":"user","content":"Hello"}],
    "stream": true
  }'</code></pre>
  <p>支持 <code>stream</code>、<code>tool_calls</code>、工具活动以 SSE 注释输出。见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/api-server/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/features/honcho">
  <h1>Honcho 用户建模</h1>
  <p><a href="https://honcho.dev" target="_blank" rel="noopener">Honcho</a>（Plastic Labs）是"辩证式用户建模"后端，会基于对话推断出一个不断更新的"你"的心理模型。作为 memory provider 插件接入：</p>
  <pre><code>hermes plugins install memory-honcho
hermes config set memory.provider honcho
hermes honcho status
hermes honcho profile         # 查看当前模型对你的"认知"</code></pre>
  <p>自托管 vs 云服务、隐私边界见 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/honcho/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="user-guide/features/provider-routing">
  <h1>Provider 路由</h1>
  <p>精细控制哪个 provider 处理哪些请求，优化成本 / 速度 / 质量。支持排序、白名单、黑名单、优先级。主要在 <code>config.yaml</code> 里：</p>
  <pre><code>routing:
  sort: price              # price | latency | quality
  allow_providers: [openrouter, anthropic]
  deny_providers: []
  per_task:
    primary: anthropic/claude-opus-4
    vision:  google/gemini-1.5-flash
    compression: openai/gpt-4o-mini</code></pre>
  <p>见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/provider-routing/" target="_blank" rel="noopener">Provider Routing</a>。</p>
</section>

<section data-page="user-guide/features/fallback-providers">
  <h1>Provider 容灾（Fallback）</h1>
  <p>主模型出错、超时、429 时自动切备份；辅助任务（视觉、压缩）有独立 fallback 链。</p>
  <pre><code>fallback:
  primary:
    - anthropic/claude-opus-4
    - openrouter/anthropic/claude-opus-4
    - openai/gpt-4o
  auxiliary:
    vision: google/gemini-1.5-flash
    compression: openai/gpt-4o-mini</code></pre>
  <p>Fallback 不重复计费已成功的 token；失败时自动写日志并切。英文原文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/fallback-providers/" target="_blank" rel="noopener">Fallback Providers</a>。</p>
</section>

<section data-page="user-guide/features/credential-pools">
  <h1>凭证池（Credential Pools）</h1>
  <p>为同一个 provider 配置多把 API key，Hermes 自动轮转 —— 命中限流、凭据失效时切下一把。</p>
  <pre><code>credentials:
  openrouter:
    pool:
      - sk-or-aaaa...
      - sk-or-bbbb...
      - sk-or-cccc...
    rotation: round-robin      # round-robin | least-used | on-error</code></pre>
  <p>日志会打码输出 key 尾部便于排查。详见英文 <a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/credential-pools/" target="_blank" rel="noopener">Credential Pools</a>。</p>
</section>

<!-- =========================================================================
  Guides & Tutorials
  ========================================================================= -->

<section data-page="guides/tips">
  <h1>使用技巧与最佳实践</h1>
  <p>一组能立刻提升你使用 Hermes Agent 效率的实用小窍门。按小节扫标题跳到你关心的就好。</p>

  <hr>

  <h2>得到好结果的方式</h2>
  <h3>把需求写具体</h3>
  <p>模糊的 prompt 得到模糊的结果。别说"修一下代码"，说"<code>api/handlers.py</code> 第 47 行 <code>process_request()</code> 会因为 <code>parse_body()</code> 返回 <code>None</code> 抛 <code>TypeError</code>，请修"。给越多上下文，迭代就越少。</p>

  <h3>一开始就把关键信息给齐</h3>
  <p>在首条消息里就把相关内容塞进去：文件路径、错误信息、期望行为。一条精心写的消息，胜过三轮来回澄清。错误 traceback 直接粘贴，Agent 能解析。</p>

  <h3>把重复性指令放进 context 文件</h3>
  <p>如果你老在重复同一套要求（"用 Tab 不用空格"、"我们用 pytest"、"API 在 <code>/api/v2</code>"），放到 <code>AGENTS.md</code>。Agent 每个会话都会自动读 —— 配一次，之后零成本。</p>

  <h3>让 Agent 用它自己的工具</h3>
  <p>别事事手把手。说"找出并修掉那个失败的测试"，而不是"先打开 <code>tests/test_foo.py</code>，看第 42 行，再……"。Agent 有文件搜索、terminal、code execution —— 让它自己探索和迭代。</p>

  <h3>复杂流程用 Skill</h3>
  <p>写长 prompt 解释怎么做某件事之前，先看看有没有 skill 做这事。<code>/skills</code> 浏览，或者直接 <code>/axolotl</code>、<code>/github-pr-workflow</code> 这样用。</p>

  <h2>CLI 进阶技巧</h2>
  <h3>多行输入</h3>
  <p><strong>Alt+Enter</strong>（或 <strong>Ctrl+J</strong>）换行不发送。多行 prompt、代码块、复杂请求都靠它。</p>
  <h3>粘贴自动识别</h3>
  <p>CLI 自动识别多行粘贴 —— 一次粘一段代码块或 traceback，不会被拆成多条消息。</p>
  <h3>打断并重定向</h3>
  <p><strong>Ctrl+C</strong> 一下打断 Agent 当前回答；再输入新消息就重定向。两秒内再按一次 Ctrl+C 则强制退出。走错路时这是你的救命稻草。</p>
  <h3><code>-c</code> 恢复会话</h3>
  <p>忘了上次说到哪？<code>hermes -c</code> 无缝续上。按标题恢复：<code>hermes -r "my research project"</code>。</p>
  <h3>剪贴板图片粘贴</h3>
  <p><strong>Ctrl+V</strong> 把剪贴板图片直接贴进对话。Agent 调视觉模型分析截图、示意图、报错弹窗、UI 草图，不用先存文件。</p>
  <h3>斜杠命令补全</h3>
  <p>输入 <code>/</code> 按 <strong>Tab</strong> 看所有命令，包括 <code>/compress</code> / <code>/model</code> / <code>/title</code> 以及所有已装 skill。不用记。</p>

  <h2>上下文管理</h2>
  <ul>
    <li>长对话快超限时用 <code>/compress</code> 手动压缩；也可降低自动压缩阈值让它更早触发</li>
    <li>用 <code>@file</code> / <code>@dir</code> / <code>@git-diff</code> 精确地只塞你要的</li>
    <li>用 <code>/new</code> 主动开新会话，别让旧上下文污染新任务</li>
  </ul>

  <h2>省钱</h2>
  <ul>
    <li>辅助任务（视觉、压缩、摘要）切到便宜模型（如 gpt-4o-mini、gemini-1.5-flash）</li>
    <li>开 provider 路由按 <code>price</code> 排序（见 <a data-page="user-guide/features/provider-routing">Provider 路由</a>）</li>
    <li>开 Anthropic / OpenAI 的 prompt caching（Hermes 默认会保住 cache key）</li>
    <li>委派调研类任务到子 Agent，减少主会话膨胀</li>
  </ul>

  <h2>安全</h2>
  <ul>
    <li>生产：<code>terminal.backend: docker</code> 或 <code>ssh</code>，别直接 <code>local</code></li>
    <li><code>approvals.mode: smart</code> 在安全性与打扰之间平衡</li>
    <li>Gateway 一定开 allowlist 或 DM pairing</li>
    <li>给每个服务单独的 Hermes profile + credential，便于吊销</li>
    <li>定期 <code>hermes backup</code></li>
  </ul>

  <h2>调试</h2>
  <ul>
    <li><code>hermes doctor</code> 先跑一遍</li>
    <li><code>hermes chat --verbose</code> 看流式详细信息</li>
    <li><code>hermes logs gateway -f</code> / <code>hermes logs errors -f</code></li>
    <li><code>hermes dump</code> 输出可复制的"环境快照"，提 issue 或发 Discord 时带上</li>
  </ul>
  <p>本页为英文原文 <a href="https://hermes-agent.nousresearch.com/docs/guides/tips/" target="_blank" rel="noopener">Tips & Best Practices</a> 的完整翻译。</p>
</section>

<section data-page="guides/local-llm-on-mac">
  <h1>在 Mac 上跑本地 LLM</h1>
  <p>指南：用 Ollama / llama.cpp / LM Studio 在 Mac（尤其是 Apple Silicon）上跑一个够用的本地模型，然后接到 Hermes。推荐：</p>
  <ul>
    <li>≥ 64GB 内存：Qwen 2.5 Coder 32B、Llama 3.3 70B（Q4）</li>
    <li>32GB 内存：Qwen 2.5 Coder 14B / Mistral Small 3</li>
    <li>16GB 内存：小模型 + 云 fallback</li>
    <li>务必把 context 设 ≥ 64K（Hermes 最低要求）</li>
  </ul>
  <pre><code># 启动 Ollama（末尾的 & 表示放入后台运行，让同一个 shell 继续配置 Hermes）
ollama run qwen2.5-coder:32b --ctx-size 65536 &amp;

hermes model
# 选 Custom endpoint: http://localhost:11434/v1  model: qwen2.5-coder:32b</code></pre>
  <p>完整基准、功耗、参数调优见 <a href="https://hermes-agent.nousresearch.com/docs/guides/local-llm-on-mac/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="guides/daily-briefing-bot">
  <h1>每日简报机器人</h1>
  <p>端到端示例：搭一个每天早上把"日历 + 邮件 + GitHub PR + 市场新闻"打包成简报发到 Telegram 的 bot。用到的模块：Telegram 平台、<code>cron</code>、<code>web_search</code>、<code>send_message</code>，以及一个 skill 把输出格式化。</p>
  <pre><code># 创建 cron
hermes cron create \
  --schedule "0 7 * * *" \
  --prompt "按每日简报 skill 整理今日日程、邮件、PR、财经新闻" \
  --skill daily-briefing \
  --deliver-to telegram:chat:&lt;id&gt;</code></pre>
  <p>完整 skill 源码与步骤：<a href="https://hermes-agent.nousresearch.com/docs/guides/daily-briefing-bot/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="guides/team-telegram-assistant">
  <h1>团队 Telegram 助手</h1>
  <p>把一个共享 Hermes 接到团队 Telegram 群里：所有成员都能 DM / @ 它做代码评审、查文档、开 cron。关键设置：</p>
  <ul>
    <li><code>TELEGRAM_ALLOWED_USERS</code> 列所有成员</li>
    <li><code>TELEGRAM_ALLOWED_CHATS</code> 限定工作群</li>
    <li>为共享机器开 <code>terminal.backend: ssh</code> 或 <code>docker</code>，每个成员共用沙箱</li>
    <li>用 <code>display.tool_progress: new</code> 减少打扰</li>
  </ul>
  <p>见 <a href="https://hermes-agent.nousresearch.com/docs/guides/team-telegram-assistant/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="guides/python-library">
  <h1>作为 Python 库使用</h1>
  <p>把 Hermes 当作 Python 库嵌入自己的服务：</p>
  <pre><code>from hermes_agent import AIAgent

agent = AIAgent(
    provider="anthropic",
    model="claude-opus-4",
    toolsets=["web", "terminal"],
)

async for event in agent.chat("Hello"):
    print(event)</code></pre>
  <p>支持同步 / 异步、工具回调拦截、自定义 session store。见 <a href="https://hermes-agent.nousresearch.com/docs/guides/python-library/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="guides/use-mcp-with-hermes">
  <h1>用好 MCP + Hermes</h1>
  <p>实战 MCP：常见 MCP server 一键接入方式、按服务器过滤、把 stdio MCP 打包进 Docker、让 Hermes 通过 <code>hermes mcp serve</code> 反向提供服务给 Claude Desktop / Cursor。完整范例与踩坑见 <a href="https://hermes-agent.nousresearch.com/docs/guides/use-mcp-with-hermes/" target="_blank" rel="noopener">英文原文</a>。配套：<a data-page="user-guide/features/mcp">MCP 基础</a>、<a data-page="reference/mcp-config-reference">MCP 配置参考</a>。</p>
</section>

<section data-page="guides/use-soul-with-hermes">
  <h1>用好 SOUL.md</h1>
  <p>如何把 <code>SOUL.md</code> 当作"Agent 身份"认真写：</p>
  <ul>
    <li>身份：Hermes 是谁 / 为谁服务</li>
    <li>价值观：诚实、简洁、保护用户</li>
    <li>禁区：不假装不知道、不编造引用</li>
    <li>语气：具体到"短句、bullet 为主、只有必要才举例"</li>
    <li>结束条件：任务完成后清楚地确认</li>
  </ul>
  <p>真实案例 + 多 persona 模板见 <a href="https://hermes-agent.nousresearch.com/docs/guides/use-soul-with-hermes/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="guides/use-voice-mode-with-hermes">
  <h1>用好 Voice Mode</h1>
  <p>实战指南：</p>
  <ul>
    <li>CLI + faster-whisper（本地免费 STT）+ Edge TTS（本地免费）作为零成本起点</li>
    <li>移动办公：Telegram 上 <code>/voice tts</code> + 中文选 MiniMax 语音</li>
    <li>Discord VC：在语音频道里直接语音头脑风暴，文本 channel 出 transcript</li>
    <li>延迟调优：<code>voice.vad.silence_ms</code>、TTS 分段</li>
  </ul>
  <p>见 <a href="https://hermes-agent.nousresearch.com/docs/guides/use-voice-mode-with-hermes/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="guides/build-a-hermes-plugin">
  <h1>构建 Hermes 插件</h1>
  <p>用 <code>hermes plugin init</code> 搭起手包，然后实现 <code>tools</code>、<code>hooks</code>、<code>memory_provider</code> 或 <code>context_engine</code> 入口点。发布到 PyPI 或 Hub 后其他人 <code>hermes plugins install &lt;name&gt;</code> 即可。见 <a data-page="developer-guide/memory-provider-plugin">记忆后端插件</a>、<a data-page="developer-guide/context-engine-plugin">Context Engine 插件</a> 以及 <a href="https://hermes-agent.nousresearch.com/docs/guides/build-a-hermes-plugin/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="guides/automate-with-cron">
  <h1>用 Cron 自动化</h1>
  <p>不只是"每天 8 点跑一次"，还可以：周一到周五检查新 issue；股票开盘前做 pre-market 简报；每 10 分钟拉一次监控并告警；月末出账单。搭配 skill 做模版化输出，搭配 <code>send_message</code> 投递到任何平台。</p>
  <p>详细语法、自然语言调度（"每 2 小时除了周末"）、失败重试策略见 <a href="https://hermes-agent.nousresearch.com/docs/guides/automate-with-cron/" target="_blank" rel="noopener">英文原文</a>。配套：<a data-page="user-guide/features/cron">Cron 特性</a>、<a data-page="guides/cron-troubleshooting">Cron 排错</a>、<a data-page="guides/automation-templates">自动化模板</a>。</p>
</section>

<section data-page="guides/automation-templates">
  <h1>自动化模板</h1>
  <p>即插即用的 cron + skill 模板：每日简报、监控告警、RSS 摘要、社媒摘要、AI 论文新鲜速报、GitHub Release 通知、日终账务对账等。见 <a href="https://hermes-agent.nousresearch.com/docs/guides/automation-templates/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="guides/cron-troubleshooting">
  <h1>Cron 排错</h1>
  <p>常见问题：cron 没触发（Gateway 没跑？schedule 写错了？时区？）；投递失败（平台未 allowlist？home 未设）；跑但结果不对（skill 版本？上下文 0？）。逐项检查：</p>
  <pre><code>hermes cron list
hermes cron show &lt;id&gt;
hermes cron run  &lt;id&gt;            # 立即手动跑
hermes logs gateway --grep cron
hermes gateway status</code></pre>
  <p>见 <a href="https://hermes-agent.nousresearch.com/docs/guides/cron-troubleshooting/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="guides/work-with-skills">
  <h1>Skills 实战</h1>
  <p>怎么用好 skill：挑对粒度、写好 <code>When to Use</code> 触发条件、把 reference 文件拆开减少 level-1 加载、版本化、发布到 Hub、让 Agent 自我改进。见 <a href="https://hermes-agent.nousresearch.com/docs/guides/work-with-skills/" target="_blank" rel="noopener">英文原文</a>，配套：<a data-page="user-guide/features/skills">Skills 系统</a>、<a data-page="developer-guide/creating-skills">编写 Skill</a>。</p>
</section>

<section data-page="guides/delegation-patterns">
  <h1>子代理委派模式</h1>
  <p>实战 pattern：Map-Reduce（并发读 50 个文件再由主 Agent 合并）、Research/Write 分离（一个子调研、一个子写稿）、安全防护（只读子 Agent 做风险操作的预判）、长跑任务外包（后台子 Agent 盯着 build）。见 <a href="https://hermes-agent.nousresearch.com/docs/guides/delegation-patterns/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="guides/github-pr-review-agent">
  <h1>GitHub PR Review Agent</h1>
  <p>把 Hermes 变成一个 PR 评审 bot：GitHub webhook 推送 → Hermes 拉 PR diff、运行测试、给结构化评论、必要时补丁 → 回写到 PR。用 worktree 保证并行多 PR 不冲突。</p>
  <pre><code>hermes webhook create --name github-pr --on-event pull_request
hermes skills install nous/skills/github-pr-workflow</code></pre>
  <p>完整搭建步骤见 <a href="https://hermes-agent.nousresearch.com/docs/guides/github-pr-review-agent/" target="_blank" rel="noopener">英文原文</a>。配套：<a data-page="guides/webhook-github-pr-review">Webhook 驱动 PR Review</a>、<a data-page="user-guide/git-worktrees">Git Worktree</a>。</p>
</section>

<section data-page="guides/webhook-github-pr-review">
  <h1>Webhook 驱动 PR Review</h1>
  <p>上一篇的 webhook 实现细节：配 GitHub App、验证 HMAC 签名、把 <code>pull_request</code> 事件映射为 Hermes prompt、用 Telegram 做"二次审核"入口（<code>/approve</code> / <code>/deny</code> 决定是否合并）。见 <a href="https://hermes-agent.nousresearch.com/docs/guides/webhook-github-pr-review/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="guides/migrate-from-openclaw">
  <h1>从 OpenClaw 迁移</h1>
  <p><a href="https://github.com/openclaw/openclaw" target="_blank" rel="noopener">OpenClaw</a> 用户迁到 Hermes 的指南：会话、skill、配置映射；<code>hermes claw import</code> 工具；差异与兼容层。见 <a href="https://hermes-agent.nousresearch.com/docs/guides/migrate-from-openclaw/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="guides/aws-bedrock">
  <h1>AWS Bedrock</h1>
  <p>把 Hermes 接到 AWS Bedrock（Claude、Llama、Titan 等）。认证走标准 AWS 凭据链（<code>AWS_ACCESS_KEY_ID</code> / <code>AWS_SECRET_ACCESS_KEY</code> / <code>AWS_PROFILE</code> / IAM role）。</p>
  <pre><code>hermes config set model.provider bedrock
hermes config set model.default anthropic.claude-opus-4-20250101-v1:0
hermes config set AWS_REGION us-west-2</code></pre>
  <p>VPC endpoint、跨区 fallback、IAM 最小权限见 <a href="https://hermes-agent.nousresearch.com/docs/guides/aws-bedrock/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<!-- =========================================================================
  Developer Guide
  ========================================================================= -->

<section data-page="developer-guide/contributing">
  <h1>参与贡献</h1>
  <p>欢迎提交 Issue / PR。开始之前：</p>
  <ol>
    <li>Fork 仓库、装开发依赖：<code>uv pip install -e ".[all,dev]"</code></li>
    <li>建开发 profile：<code>hermes profile create dev</code>，避免弄脏日常配置</li>
    <li>跑一次测试：<code>pytest</code>；type-check：<code>mypy</code></li>
    <li>小而聚焦的 PR 比大而全更容易合</li>
    <li>按 <code>CONTRIBUTING.md</code> 写 commit message，附改动动机</li>
  </ol>
  <p>详见英文 <a href="https://hermes-agent.nousresearch.com/docs/developer-guide/contributing/" target="_blank" rel="noopener">Contributing</a>。</p>
</section>

<section data-page="developer-guide/architecture">
  <h1>系统架构</h1>
  <p>这一页是 Hermes Agent 内部的顶层地图。用它定位，然后再去看对应子系统的文档。</p>

  <h2>系统总览</h2>
  <p>几个入口（CLI / Gateway / ACP / Batch / API Server / Python Library）都通向 <code>AIAgent</code>（<code>run_agent.py</code>），其核心由以下模块构成：</p>
  <ul>
    <li><strong>Prompt Builder</strong>（<code>prompt_builder.py</code>）—— 组装 system prompt</li>
    <li><strong>Provider Resolution</strong>（<code>runtime_provider.py</code>）—— 解析 provider / model</li>
    <li><strong>Tool Dispatch</strong>（<code>model_tools.py</code>）—— 工具调度</li>
    <li><strong>Compression & Caching</strong> —— 上下文压缩 + Anthropic prompt cache</li>
    <li><strong>3 API 模式</strong> —— chat completions、codex responses、Anthropic 原生</li>
    <li><strong>Tool Registry</strong>（<code>registry.py</code>）—— 47 个 tool / 19 个 toolset</li>
  </ul>
  <p>下层再接：</p>
  <ul>
    <li><strong>会话存储</strong>：SQLite + FTS5（<code>hermes_state.py</code>、<code>gateway/session.py</code>）</li>
    <li><strong>工具后端</strong>：Terminal 6 种后端、Browser 5 种后端、Web 4 种后端、MCP 动态、File/Vision 等</li>
  </ul>

  <h2>目录结构</h2>
  <pre><code>hermes-agent/
├── run_agent.py              # AIAgent — 对话主循环（~10,700 行）
├── cli.py                    # HermesCLI — 交互终端 UI（~10,000 行）
├── model_tools.py            # 工具发现、schema 收集、分发
├── toolsets.py               # 工具分组与平台预设
├── hermes_state.py           # SQLite 会话/状态库 + FTS5
├── hermes_constants.py       # HERMES_HOME、profile 相关路径
├── batch_runner.py           # 批量 trajectory 生成
│
├── agent/                    # Agent 内部
│   ├── prompt_builder.py     # system prompt 组装
│   ├── context_engine.py     # ContextEngine 抽象基类（可插拔）
│   ├── context_compressor.py # 默认实现 — 有损摘要
│   ├── prompt_caching.py     # Anthropic prompt caching
│   ├── auxiliary_client.py   # 辅助 LLM（视觉、摘要）
│   ├── model_metadata.py     # 模型 context 长度、token 估算
│   ├── models_dev.py         # models.dev 注册表集成
│   └── …
│
├── tools/                    # 工具实现
├── gateway/                  # 消息 Gateway
├── adapters/                 # 平台适配器（Telegram / Discord / …）
├── plugins/                  # 内置插件
├── skills/                   # 内置 skills
├── acp_adapter/              # ACP 编辑器桥
├── cli_tui/                  # TUI (TypeScript)
└── tests/</code></pre>

  <h2>学习闭环</h2>
  <p>Hermes 的"self-improving"有四个机制协同：</p>
  <ul>
    <li><strong>MEMORY.md / USER.md</strong> —— Agent 自己管理的、有边界的跨会话笔记（<a data-page="user-guide/features/memory">Memory</a>）</li>
    <li><strong>Skills</strong> —— Agent 通过 <code>skill_manage</code> 把反复出现的流程固化成过程性知识（<a data-page="user-guide/features/skills">Skills</a>）</li>
    <li><strong>session_search</strong> —— FTS5 索引让 Agent 按需检索自己过往会话，再由辅助 LLM 总结</li>
    <li><strong>Honcho / 外部 memory provider</strong>（可选）—— 辩证式用户建模（<a data-page="user-guide/features/memory-providers">Memory Providers</a>）</li>
  </ul>

  <h2>工具运行时简述</h2>
  <p><code>model_tools.py</code> 汇集所有启用工具的 JSON schema 发给模型；模型返回 tool_call 后，经 <code>run_agent.py</code> 验参、做审批、调工具实现（在 <code>tools/</code>），并把结果塞回会话，进入下一轮推理。</p>

  <h2>看得更深的页</h2>
  <ul>
    <li><a data-page="developer-guide/agent-loop">Agent 主循环</a></li>
    <li><a data-page="developer-guide/prompt-assembly">Prompt 组装</a></li>
    <li><a data-page="developer-guide/context-compression-and-caching">上下文压缩与缓存</a></li>
    <li><a data-page="developer-guide/gateway-internals">Gateway 内部</a></li>
    <li><a data-page="developer-guide/session-storage">会话存储</a></li>
    <li><a data-page="developer-guide/provider-runtime">Provider 运行时</a></li>
    <li><a data-page="developer-guide/tools-runtime">工具运行时</a></li>
  </ul>
  <p>本页对照英文 <a href="https://hermes-agent.nousresearch.com/docs/developer-guide/architecture/" target="_blank" rel="noopener">Architecture</a> 翻译，保留全部模块命名。</p>
</section>

<section data-page="developer-guide/agent-loop">
  <h1>Agent 主循环</h1>
  <p><code>run_agent.py</code> 里的 <code>AIAgent.run()</code> 是会话的核心循环：</p>
  <ol>
    <li>组装 system prompt（身份 / context / memory / skills / toolset schema）</li>
    <li>调 provider：chat completions / codex responses / anthropic native 三种 transport 之一</li>
    <li>流式解析输出；遇到 tool_call 则暂停流、执行工具、把结果 append 为 <code>tool</code> 消息</li>
    <li>必要时触发压缩（按 token 阈值），或 prompt caching（Anthropic）</li>
    <li>遇到 <code>stop</code> / 结束条件 / 用户打断则退出循环</li>
  </ol>
  <p>失败时走 fallback 链（<a data-page="user-guide/features/fallback-providers">Fallback Providers</a>），429 走 credential pool 轮转（<a data-page="user-guide/features/credential-pools">Credential Pools</a>）。英文：<a href="https://hermes-agent.nousresearch.com/docs/developer-guide/agent-loop/" target="_blank" rel="noopener">Agent Loop</a>。</p>
</section>

<section data-page="developer-guide/prompt-assembly">
  <h1>Prompt 组装</h1>
  <p>system prompt 按"槽位"拼装（会话开始时冻结，保 prefix cache）：</p>
  <ol>
    <li>SOUL.md（身份）</li>
    <li>运行时信息（日期、OS、工作目录、model capabilities）</li>
    <li>Skin 品牌 / 启动横幅</li>
    <li>Context 文件（.hermes.md / AGENTS.md 等）</li>
    <li>MEMORY.md 快照</li>
    <li>USER.md 快照</li>
    <li>预加载 skill（启动时 <code>-s</code> 指定）</li>
    <li>Toolset 规则（平台预设）</li>
    <li>安全策略（approvals 模式）</li>
  </ol>
  <p>详细字段见 <a href="https://hermes-agent.nousresearch.com/docs/developer-guide/prompt-assembly/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="developer-guide/context-compression-and-caching">
  <h1>上下文压缩与缓存</h1>
  <p>压缩：当 token 占用超过阈值，Hermes 调用辅助 LLM 把"旧消息窗口"摘成结构化摘要 + 保留关键引用 + 保留最后 N 条原文。缓存：Anthropic 原生 API 支持 <code>cache_control</code>，Hermes 让 system prompt 与工具 schema 保持稳定以命中 cache；节省 50–90% 输入 token。详细算法 / 调参见 <a href="https://hermes-agent.nousresearch.com/docs/developer-guide/context-compression-and-caching/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="developer-guide/gateway-internals">
  <h1>Gateway 内部</h1>
  <p>Gateway 是个 asyncio 主循环，跑若干子任务：每个平台 adapter 一个、cron scheduler 一个、后台进程 watcher 一个。session store 按 <code>(platform, chat_id)</code> 作为主键维护会话状态，落盘到 SQLite。完整类图见 <a href="https://hermes-agent.nousresearch.com/docs/developer-guide/gateway-internals/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="developer-guide/session-storage">
  <h1>会话存储</h1>
  <p>SQLite 数据库 <code>~/.hermes/state.db</code>，核心表：<code>sessions</code>、<code>messages</code>、<code>tool_calls</code>、<code>checkpoints</code>；FTS5 虚拟表做全文检索支撑 <code>session_search</code>。压缩 / 导出 / 迁移脚本见 <a href="https://hermes-agent.nousresearch.com/docs/developer-guide/session-storage/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="developer-guide/provider-runtime">
  <h1>Provider 运行时</h1>
  <p><code>runtime_provider.py</code> 负责把"逻辑 provider/model"解析为一个具体 client：包括 transport 选择（openai-wire / anthropic-native / codex-responses）、credential pool、超时策略、fallback 链、routing。新增 provider 见 <a data-page="developer-guide/adding-providers">新增 Provider</a>。</p>
</section>

<section data-page="developer-guide/adding-tools">
  <h1>新增工具</h1>
  <p>一个 tool 是一个带类型注解的 Python 函数，加 <code>@tool</code> 装饰器、可选 <code>@requires_approval</code>。schema 从注解自动生成。把模块放 <code>tools/</code> 或做成 plugin，重启 Hermes 即可。</p>
  <pre><code>from hermes_agent.tools import tool

@tool(toolset="custom")
def my_tool(query: str, limit: int = 10) -> dict:
    """一句话描述：干啥的，什么时候用。"""
    ...</code></pre>
  <p>细节：危险参数检测、流式进度、后台进程对接、error 处理风格，见 <a href="https://hermes-agent.nousresearch.com/docs/developer-guide/adding-tools/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="developer-guide/adding-providers">
  <h1>新增 Provider</h1>
  <p>继承 <code>BaseProvider</code> 或直接复用 <code>OpenAIWireProvider</code>（OpenAI 兼容）。需要实现：认证、模型清单、<code>chat_completions</code> / <code>responses</code> / <code>messages</code> 之一、流式解析、token 计数。见 <a href="https://hermes-agent.nousresearch.com/docs/developer-guide/adding-providers/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="developer-guide/adding-platform-adapters">
  <h1>新增平台适配器</h1>
  <p>平台 adapter 继承 <code>PlatformAdapter</code>，把平台消息 → <code>InboundMessage</code>，把 Agent 输出 → 平台消息。需要实现：连接 / 心跳、消息收发、附件上下行、typing、流式编辑（可选）、voice（可选）、reaction（可选）、allowlist / 鉴权。示例与测试规范见 <a href="https://hermes-agent.nousresearch.com/docs/developer-guide/adding-platform-adapters/" target="_blank" rel="noopener">英文原文</a>。</p>
</section>

<section data-page="developer-guide/memory-provider-plugin">
  <h1>记忆后端插件</h1>
  <p>实现 <code>MemoryProvider</code> 接口：<code>write</code>、<code>retrieve</code>、<code>system_prompt_block</code>（可选）。内置 Honcho / Mem0 等都是这种形式。英文：<a href="https://hermes-agent.nousresearch.com/docs/developer-guide/memory-provider-plugin/" target="_blank" rel="noopener">Memory Provider Plugin</a>。</p>
</section>

<section data-page="developer-guide/context-engine-plugin">
  <h1>Context Engine 插件</h1>
  <p>默认 Context Engine 做"有损摘要"压缩。你可以换成 sliding window、semantic cache、long-term knowledge graph 等策略。实现 <code>ContextEngine</code> ABC 的 <code>should_compress</code>、<code>compress</code>、<code>render</code>。英文：<a href="https://hermes-agent.nousresearch.com/docs/developer-guide/context-engine-plugin/" target="_blank" rel="noopener">Context Engine Plugin</a>。</p>
</section>

<section data-page="developer-guide/creating-skills">
  <h1>编写 Skill</h1>
  <p>一个 skill 就是一个文件夹：<code>SKILL.md</code>（必选）+ 可选 reference 文件 / 脚本。<code>SKILL.md</code> 里用 frontmatter 指定 name / description / tags / platforms / requires_toolsets 等（见 <a data-page="user-guide/features/skills">Skills 系统</a>）。发布：</p>
  <pre><code>hermes skills publish ./my-skill</code></pre>
  <p>写作建议：触发条件要具体、示例要可直接复制跑、操作要幂等、失败要给出排错步骤。英文：<a href="https://hermes-agent.nousresearch.com/docs/developer-guide/creating-skills/" target="_blank" rel="noopener">Creating Skills</a>。</p>
</section>

<section data-page="developer-guide/extending-the-cli">
  <h1>扩展 CLI</h1>
  <p>通过插件注册新斜杠命令、自定义欢迎 banner、添加 autocompletion、勾 hook 到对话生命周期。英文：<a href="https://hermes-agent.nousresearch.com/docs/developer-guide/extending-the-cli/" target="_blank" rel="noopener">Extending the CLI</a>。</p>
</section>

<section data-page="developer-guide/tools-runtime">
  <h1>工具运行时</h1>
  <p><code>model_tools.py</code> 负责发现工具（内置 + 启用插件 + MCP 动态）、收集 schema 发给模型、解析模型返回的 tool_call、在需要时做审批、调 tool 实现、捕获异常变友好错误。支持流式进度、后台任务、PTY 会话。英文：<a href="https://hermes-agent.nousresearch.com/docs/developer-guide/tools-runtime/" target="_blank" rel="noopener">Tools Runtime</a>。</p>
</section>

<section data-page="developer-guide/acp-internals">
  <h1>ACP 内部</h1>
  <p>ACP 走 JSON-RPC over stdio：编辑器起 <code>hermes acp</code> 子进程，双向收发 <code>chat</code>、<code>tool_call</code>、<code>tool_result</code>、<code>file_diff</code>、<code>terminal</code>。与 Hermes 核心的粘合层在 <code>acp_adapter/</code>。英文：<a href="https://hermes-agent.nousresearch.com/docs/developer-guide/acp-internals/" target="_blank" rel="noopener">ACP Internals</a>。</p>
</section>

<section data-page="developer-guide/cron-internals">
  <h1>Cron 内部</h1>
  <p>Cron 调度器每 60 秒 tick 一次，扫描 <code>~/.hermes/cron/</code> 下的 YAML 文件，命中的 job 构造一次 Agent 会话运行（独立 session、独立 prompt），完成后按 <code>deliver_to</code> 路由结果。自然语言调度由辅助 LLM 把"每天早 8 点"解析为 cron 表达式。英文：<a href="https://hermes-agent.nousresearch.com/docs/developer-guide/cron-internals/" target="_blank" rel="noopener">Cron Internals</a>。</p>
</section>

<section data-page="developer-guide/environments">
  <h1>Environments</h1>
  <p>Environment 是一组"背景能力"的抽象（工作目录、terminal 后端、可用工具、环境变量）。CLI / Gateway / Cron / Batch 会分别创建不同 environment。英文：<a href="https://hermes-agent.nousresearch.com/docs/developer-guide/environments/" target="_blank" rel="noopener">Environments</a>。</p>
</section>

<section data-page="developer-guide/trajectory-format">
  <h1>Trajectory 格式</h1>
  <p>Hermes 把每个会话序列化为 ShareGPT 风格的 JSONL：每一行一个完整会话，字段包括 <code>messages</code>、<code>tools</code>、<code>tool_calls</code>、<code>metadata</code>（model / provider / cost / tokens）、<code>outcome</code>（success / error / interrupted）。可直接喂给 SFT、DPO、Atropos 等训练栈。英文：<a href="https://hermes-agent.nousresearch.com/docs/developer-guide/trajectory-format/" target="_blank" rel="noopener">Trajectory Format</a>。</p>
</section>

<!-- =========================================================================
  Reference
  ========================================================================= -->

<section data-page="reference/cli-commands">
  <h1>CLI 命令参考</h1>
  <p>本页覆盖你从 shell 里跑的 <strong>terminal 命令</strong>。会话内的斜杠命令见 <a data-page="reference/slash-commands">斜杠命令参考</a>。</p>

  <h2>全局入口</h2>
  <pre><code>hermes [全局选项] &lt;command&gt; [子命令/选项]</code></pre>

  <h3>全局选项</h3>
  <table>
    <thead><tr><th>选项</th><th>说明</th></tr></thead>
    <tbody>
      <tr><td><code>--version</code>、<code>-V</code></td><td>显示版本并退出</td></tr>
      <tr><td><code>--profile &lt;name&gt;</code>、<code>-p</code></td><td>本次调用使用的 Hermes profile；覆盖 <code>hermes profile use</code> 设的默认</td></tr>
      <tr><td><code>--resume &lt;session&gt;</code>、<code>-r</code></td><td>按 ID 或标题恢复会话</td></tr>
      <tr><td><code>--continue [name]</code>、<code>-c</code></td><td>恢复最近一次会话，或匹配标题的最近一次</td></tr>
      <tr><td><code>--worktree</code>、<code>-w</code></td><td>在隔离的 git worktree 里启动（并行代理）</td></tr>
      <tr><td><code>--yolo</code></td><td>绕过危险命令审批</td></tr>
      <tr><td><code>--pass-session-id</code></td><td>把 session ID 塞进 system prompt</td></tr>
      <tr><td><code>--ignore-user-config</code></td><td>忽略 <code>~/.hermes/config.yaml</code>，回退内置默认；<code>.env</code> 里的凭据仍加载</td></tr>
      <tr><td><code>--ignore-rules</code></td><td>跳过自动注入 <code>AGENTS.md</code> / <code>SOUL.md</code> / <code>.cursorrules</code> / memory / 预加载 skill</td></tr>
      <tr><td><code>--tui</code></td><td>启动 <a data-page="user-guide/tui">TUI</a>；等价 <code>HERMES_TUI=1</code></td></tr>
      <tr><td><code>--dev</code></td><td>与 <code>--tui</code> 搭配：用 <code>tsx</code> 跑 TS 源码（TUI 贡献者用）</td></tr>
    </tbody>
  </table>

  <h2>顶层命令</h2>
  <table>
    <thead><tr><th>命令</th><th>用途</th></tr></thead>
    <tbody>
      <tr><td><code>hermes chat</code></td><td>交互式或一次性对话</td></tr>
      <tr><td><code>hermes model</code></td><td>交互式选 provider + model</td></tr>
      <tr><td><code>hermes gateway</code></td><td>管理消息 Gateway 服务</td></tr>
      <tr><td><code>hermes setup</code></td><td>交互式一站式配置</td></tr>
      <tr><td><code>hermes whatsapp</code></td><td>配对 WhatsApp 桥接</td></tr>
      <tr><td><code>hermes auth</code></td><td>凭据管理（add / list / remove / reset / set strategy）；处理 Codex / Nous / Anthropic OAuth</td></tr>
      <tr><td><code>hermes login</code> / <code>logout</code></td><td><strong>已废弃</strong> —— 用 <code>hermes auth</code></td></tr>
      <tr><td><code>hermes status</code></td><td>Agent / 认证 / 平台状态</td></tr>
      <tr><td><code>hermes cron</code></td><td>查看和 tick 定时调度</td></tr>
      <tr><td><code>hermes webhook</code></td><td>动态 webhook 订阅</td></tr>
      <tr><td><code>hermes doctor</code></td><td>自检</td></tr>
      <tr><td><code>hermes dump</code></td><td>可复制的环境快照（给支持 / 调试）</td></tr>
      <tr><td><code>hermes debug</code></td><td>上传日志 + 系统信息</td></tr>
      <tr><td><code>hermes backup</code> / <code>hermes import</code></td><td>备份 / 恢复 <code>~/.hermes</code></td></tr>
      <tr><td><code>hermes logs</code></td><td>查看、tail、筛 agent / gateway / error 日志</td></tr>
      <tr><td><code>hermes config</code></td><td>查看、编辑、迁移、查询配置</td></tr>
      <tr><td><code>hermes pairing</code></td><td>审批 / 撤销消息平台配对码</td></tr>
      <tr><td><code>hermes skills</code></td><td>skill 的浏览、安装、发布、审计、配置</td></tr>
      <tr><td><code>hermes honcho</code></td><td>Honcho 记忆集成</td></tr>
      <tr><td><code>hermes memory</code></td><td>外部 memory provider</td></tr>
      <tr><td><code>hermes acp</code></td><td>以 ACP server 形式运行</td></tr>
      <tr><td><code>hermes mcp</code></td><td>MCP 管理 / 以 MCP server 形式运行</td></tr>
      <tr><td><code>hermes plugins</code></td><td>插件安装 / 启用 / 禁用 / 删除</td></tr>
      <tr><td><code>hermes tools</code></td><td>按平台配置启用的工具</td></tr>
      <tr><td><code>hermes sessions</code></td><td>会话浏览 / 导出 / 清理 / 改名 / 删除</td></tr>
      <tr><td><code>hermes insights</code></td><td>token / 成本 / 活动分析</td></tr>
      <tr><td><code>hermes claw</code></td><td>OpenClaw 迁移助手</td></tr>
      <tr><td><code>hermes dashboard</code></td><td>启动 Web Dashboard</td></tr>
      <tr><td><code>hermes profile</code></td><td>多实例 profile 管理</td></tr>
      <tr><td><code>hermes completion</code></td><td>bash / zsh 补全脚本</td></tr>
      <tr><td><code>hermes version</code></td><td>版本信息</td></tr>
      <tr><td><code>hermes update</code></td><td>拉最新代码 + 重装依赖</td></tr>
      <tr><td><code>hermes uninstall</code></td><td>卸载</td></tr>
    </tbody>
  </table>

  <h2><code>hermes chat</code></h2>
  <p>常用选项：<code>--model</code>、<code>--provider</code>、<code>--toolsets</code>、<code>-q</code>（单次 query）、<code>-s</code>（预加载 skill）、<code>--continue</code> / <code>--resume</code>、<code>--verbose</code>、<code>--worktree</code>、<code>--yolo</code>。</p>

  <h2><code>hermes gateway</code></h2>
  <p>子命令：<code>setup</code>、<code>install</code>（<code>--system</code>）、<code>start</code>、<code>stop</code>、<code>status</code>、<code>setup</code>。详见 <a data-page="user-guide/messaging/index">Messaging 总览</a>。</p>

  <h2><code>hermes config</code></h2>
  <pre><code>hermes config                 # 查看
hermes config edit
hermes config set KEY VAL
hermes config unset KEY
hermes config check
hermes config migrate</code></pre>

  <h2><code>hermes sessions</code></h2>
  <pre><code>hermes sessions list [--recent 20] [--profile NAME]
hermes sessions search "关键词"
hermes sessions export &lt;id&gt; --format sharegpt
hermes sessions rename &lt;id&gt; "名字"
hermes sessions delete &lt;id&gt;
hermes sessions prune --older-than 30d</code></pre>

  <h2><code>hermes skills</code></h2>
  <pre><code>hermes skills
hermes skills list
hermes skills search kubernetes
hermes skills install &lt;name|path|url&gt;
hermes skills remove &lt;name&gt;
hermes skills publish ./my-skill
hermes skills audit</code></pre>

  <h2><code>hermes cron</code></h2>
  <pre><code>hermes cron list
hermes cron show &lt;id&gt;
hermes cron create --schedule "0 8 * * *" --prompt "..." --deliver-to telegram:chat:&lt;id&gt;
hermes cron pause &lt;id&gt;
hermes cron resume &lt;id&gt;
hermes cron run &lt;id&gt;
hermes cron remove &lt;id&gt;
hermes cron tick</code></pre>

  <p>其余子命令（<code>webhook</code>、<code>auth</code>、<code>plugins</code>、<code>tools</code> 等）的全部选项见英文原文 <a href="https://hermes-agent.nousresearch.com/docs/reference/cli-commands/" target="_blank" rel="noopener">CLI Commands Reference</a>。</p>
</section>

<section data-page="reference/slash-commands">
  <h1>斜杠命令参考</h1>
  <p>这些命令在 CLI / TUI / 任何消息平台的会话里都可以用。</p>
  <h2>会话</h2>
  <p><code>/help</code> / <code>/new</code> / <code>/reset</code> / <code>/status</code> / <code>/title [name]</code> / <code>/resume [name]</code> / <code>/undo</code> / <code>/retry</code> / <code>/stop</code> / <code>/sethome</code> / <code>/save</code> / <code>/compress</code> / <code>/usage</code> / <code>/insights [days]</code>。</p>
  <h2>模型 / 推理</h2>
  <p><code>/model [provider:model]</code> / <code>/provider</code> / <code>/reasoning [level|show|hide]</code> / <code>/personality [name]</code>。</p>
  <h2>工具 / 工作流</h2>
  <p><code>/tools</code> / <code>/skills</code> / <code>/memory</code> / <code>/rollback [n]</code> / <code>/background &lt;prompt&gt;</code> / <code>/reload-mcp</code> / <code>/update</code> / <code>/approve</code> / <code>/deny</code> / <code>/yolo</code>。</p>
  <h2>语音</h2>
  <p><code>/voice on|off|tts|join|leave|status</code>。</p>
  <h2>动态</h2>
  <p><code>/&lt;skill-name&gt; [args]</code> —— 任何已安装 skill。</p>
  <p>每个命令的完整参数见英文 <a href="https://hermes-agent.nousresearch.com/docs/reference/slash-commands/" target="_blank" rel="noopener">Slash Commands Reference</a>。</p>
</section>

<section data-page="reference/profile-commands">
  <h1>Profile 命令</h1>
  <pre><code>hermes profile list
hermes profile create &lt;name&gt;
hermes profile use &lt;name&gt;          # 设为默认（粘性）
hermes profile remove &lt;name&gt;
hermes -p &lt;name&gt; chat               # 单次使用某 profile，不改默认</code></pre>
  <p>每个 profile 对应 <code>~/.hermes-&lt;name&gt;/</code>，会话 / Gateway 服务名 / 端口独立。详见英文 <a href="https://hermes-agent.nousresearch.com/docs/reference/profile-commands/" target="_blank" rel="noopener">Profile Commands</a>。</p>
</section>

<section data-page="reference/environment-variables">
  <h1>环境变量</h1>
  <p>全部环境变量（按类别）：</p>
  <h2>核心</h2>
  <ul>
    <li><code>HERMES_HOME</code> —— 默认 <code>~/.hermes</code></li>
    <li><code>HERMES_PROFILE</code> —— 覆盖默认 profile</li>
    <li><code>HERMES_YOLO_MODE=1</code> —— 绕过审批</li>
    <li><code>HERMES_TUI=1</code> —— 默认启动 TUI</li>
    <li><code>HERMES_BACKGROUND_NOTIFICATIONS=result</code> —— 见 <a data-page="user-guide/messaging/index">Messaging</a></li>
  </ul>
  <h2>Provider</h2>
  <ul>
    <li><code>NOUS_API_KEY</code>、<code>OPENROUTER_API_KEY</code>、<code>OPENAI_API_KEY</code>、<code>ANTHROPIC_API_KEY</code>、<code>GOOGLE_API_KEY</code>、<code>ZHIPU_API_KEY</code>、<code>MOONSHOT_API_KEY</code>、<code>MINIMAX_API_KEY</code>、<code>DEEPSEEK_API_KEY</code>、<code>AZURE_OPENAI_*</code>、<code>AWS_*</code>……</li>
    <li><code>SUDO_PASSWORD</code>（缓存 sudo 密码）</li>
  </ul>
  <h2>平台</h2>
  <ul>
    <li><code>TELEGRAM_BOT_TOKEN</code> / <code>TELEGRAM_ALLOWED_USERS</code> / <code>TELEGRAM_ALLOWED_CHATS</code></li>
    <li><code>DISCORD_BOT_TOKEN</code> / <code>DISCORD_ALLOWED_USERS</code></li>
    <li><code>SLACK_BOT_TOKEN</code> / <code>SLACK_APP_TOKEN</code> / <code>SLACK_SIGNING_SECRET</code> / <code>SLACK_ALLOWED_USERS</code></li>
    <li>Signal / SMS / Email / Mattermost / Matrix / DingTalk / Feishu / WeCom / Weixin / BlueBubbles / QQ …</li>
    <li><code>GATEWAY_ALLOWED_USERS</code> / <code>GATEWAY_ALLOW_ALL_USERS</code></li>
  </ul>
  <h2>Terminal</h2>
  <ul>
    <li><code>TERMINAL_SSH_HOST</code> / <code>TERMINAL_SSH_USER</code> / <code>TERMINAL_SSH_KEY</code></li>
    <li><code>DAYTONA_API_KEY</code> / <code>MODAL_TOKEN_ID</code> / <code>MODAL_TOKEN_SECRET</code></li>
  </ul>
  <h2>超时与调试</h2>
  <ul>
    <li><code>HERMES_API_TIMEOUT</code>（遗留，已被 config 覆盖）</li>
    <li><code>HERMES_API_CALL_STALE_TIMEOUT</code>（同上）</li>
    <li><code>HERMES_LOG_LEVEL</code> / <code>HERMES_DEBUG=1</code></li>
  </ul>
  <p>完整 key-by-key 列表（含默认值、作用域、与 <code>config.yaml</code> 的映射关系）见英文 <a href="https://hermes-agent.nousresearch.com/docs/reference/environment-variables/" target="_blank" rel="noopener">Environment Variables</a>。</p>
</section>

<section data-page="reference/tools-reference">
  <h1>工具参考</h1>
  <p>从代码生成的权威工具清单。核心工具：</p>
  <ul>
    <li><strong>Terminal & 文件</strong>：<code>terminal</code>、<code>process</code>、<code>read_file</code>、<code>patch</code>、<code>search_files</code>、<code>list_files</code></li>
    <li><strong>Web</strong>：<code>web_search</code>、<code>web_extract</code></li>
    <li><strong>浏览器</strong>：<code>browser_navigate</code>、<code>browser_click</code>、<code>browser_type</code>、<code>browser_snapshot</code>、<code>browser_vision</code>、<code>browser_download</code></li>
    <li><strong>视觉 / 媒体</strong>：<code>vision_analyze</code>、<code>image_generate</code>、<code>text_to_speech</code></li>
    <li><strong>记忆 / 会话</strong>：<code>memory</code>、<code>session_search</code></li>
    <li><strong>规划 / 编排</strong>：<code>todo</code>、<code>clarify</code>、<code>execute_code</code>、<code>delegate_task</code></li>
    <li><strong>自动化 / 投递</strong>：<code>cronjob</code>、<code>send_message</code></li>
    <li><strong>Skill 管理</strong>：<code>skills_list</code>、<code>skill_view</code>、<code>skill_manage</code></li>
    <li><strong>Home Assistant</strong>：<code>ha_list_entities</code>、<code>ha_get_state</code>、<code>ha_call_service</code>、<code>ha_list_services</code></li>
    <li><strong>RL</strong>：<code>rl_*</code> 族</li>
    <li><strong>MCP 动态</strong>：每个连上的 MCP server 都会注册一组 <code>mcp-&lt;server&gt;/&lt;tool&gt;</code></li>
  </ul>
  <p>每个工具的参数 / 返回 / 审批策略 / 是否可后台 见英文 <a href="https://hermes-agent.nousresearch.com/docs/reference/tools-reference/" target="_blank" rel="noopener">Tools Reference</a>（代码驱动，会随版本更新）。</p>
</section>

<section data-page="reference/toolsets-reference">
  <h1>工具集参考</h1>
  <p>常见 toolset：<code>web</code> / <code>terminal</code> / <code>file</code> / <code>browser</code> / <code>vision</code> / <code>image_gen</code> / <code>moa</code> / <code>skills</code> / <code>tts</code> / <code>todo</code> / <code>memory</code> / <code>session_search</code> / <code>cronjob</code> / <code>code_execution</code> / <code>delegation</code> / <code>clarify</code> / <code>homeassistant</code> / <code>rl</code>。</p>
  <p>平台预设：<code>hermes-cli</code> / <code>hermes-telegram</code> / <code>hermes-discord</code> / <code>hermes-slack</code> / <code>hermes-whatsapp</code> / <code>hermes-signal</code> / <code>hermes-sms</code> / <code>hermes-email</code> / <code>hermes-homeassistant</code> / <code>hermes-mattermost</code> / <code>hermes-matrix</code> / <code>hermes-dingtalk</code> / <code>hermes-feishu</code> / <code>hermes-wecom</code> / <code>hermes-wecom-callback</code> / <code>hermes-weixin</code> / <code>hermes-bluebubbles</code> / <code>hermes-qqbot</code> / <code>hermes</code>（API Server 默认） / <code>hermes-webhook</code>。</p>
  <p>MCP 动态 toolset：<code>mcp-&lt;server&gt;</code>。完整 mapping（每个 toolset 展开成哪些 tool）见英文 <a href="https://hermes-agent.nousresearch.com/docs/reference/toolsets-reference/" target="_blank" rel="noopener">Toolsets Reference</a>。</p>
</section>

<section data-page="reference/mcp-config-reference">
  <h1>MCP 配置参考</h1>
  <pre><code>mcp_servers:
  &lt;name&gt;:
    # stdio 模式（command 必填）
    command: "npx"
    args: ["-y", "..."]
    env: { KEY: "value" }
    cwd: "/optional"
    # 或者 HTTP 模式（url 必填）
    url: "https://example.com/mcp"
    headers: { Authorization: "Bearer ${TOKEN}" }
    # 通用
    enabled: true
    allow_tools: ["get_*", "list_*"]
    deny_tools:  ["delete_*"]
    allow_resources: true
    allow_prompts:   true
    startup_timeout: 30
    request_timeout: 60</code></pre>
  <p>字段含义、优先级、调试方法见英文 <a href="https://hermes-agent.nousresearch.com/docs/reference/mcp-config-reference/" target="_blank" rel="noopener">MCP Config Reference</a>。</p>
</section>

<section data-page="reference/skills-catalog">
  <h1>内置 Skills 清单</h1>
  <p>Hermes 仓库 <code>skills/</code> 下随版本附带的官方 skill（首次安装自动拷到 <code>~/.hermes/skills/</code>）。常见包括：</p>
  <ul>
    <li><code>plan</code> —— 生成实施计划而非直接执行</li>
    <li><code>github-pr-workflow</code> —— 标准 PR 流程</li>
    <li><code>hermes-agent-dev</code> —— 开发 Hermes 自身</li>
    <li><code>github-auth</code> —— GitHub 凭据辅助</li>
    <li><code>axolotl</code> —— Axolotl 微调助手</li>
    <li><code>excalidraw</code> —— 生成 Excalidraw 图</li>
    <li><code>gif-search</code> —— GIF 检索</li>
    <li><code>daily-briefing</code> —— 每日简报</li>
  </ul>
  <p>完整清单（版本会变）见英文 <a href="https://hermes-agent.nousresearch.com/docs/reference/skills-catalog/" target="_blank" rel="noopener">Skills Catalog</a>。</p>
</section>

<section data-page="reference/optional-skills-catalog">
  <h1>可选 Skills 清单</h1>
  <p>官方维护、按需装的 skill（来自 Nous 及社区）：</p>
  <ul>
    <li><code>godmode</code>（见 <a data-page="user-guide/skills/godmode">Godmode</a>）</li>
    <li><code>google-workspace</code>（Gmail / Drive / Docs / Calendar / Sheets，见 <a data-page="user-guide/skills/google-workspace">Google Workspace</a>）</li>
    <li>云服务运维类：<code>k8s</code>、<code>aws</code>、<code>terraform</code></li>
    <li>研究类：<code>arxiv</code>、<code>papers-with-code</code></li>
    <li>监控类：<code>grafana</code>、<code>prometheus</code></li>
    <li>交付类：<code>deploy-fly</code>、<code>deploy-vercel</code></li>
  </ul>
  <pre><code>hermes skills search &lt;keyword&gt;
hermes skills install &lt;name&gt;</code></pre>
  <p>完整 curated 列表 + 作者 + 推荐度见英文 <a href="https://hermes-agent.nousresearch.com/docs/reference/optional-skills-catalog/" target="_blank" rel="noopener">Optional Skills Catalog</a>。</p>
</section>

<section data-page="reference/faq">
  <h1>FAQ 与故障排查</h1>
  <p>常见问题的快速答案与修复。</p>

  <hr>

  <h2>常见问题</h2>

  <h3>Hermes 支持哪些 LLM provider？</h3>
  <p>理论上任何 OpenAI 兼容 API。已测试：</p>
  <ul>
    <li><strong>OpenRouter</strong> —— 一把 key 调几百个模型（推荐，灵活）</li>
    <li><strong>Nous Portal</strong> —— Nous Research 自己的推理端点</li>
    <li><strong>OpenAI</strong> —— GPT-4o / o1 / o3 等</li>
    <li><strong>Anthropic</strong> —— Claude（原生 API，或经 OpenRouter / 兼容代理）</li>
    <li><strong>Google</strong> —— Gemini（经 OpenRouter 或兼容代理）</li>
    <li><strong>z.ai / ZhipuAI</strong> —— GLM 系列</li>
    <li><strong>Kimi / Moonshot</strong></li>
    <li><strong>MiniMax</strong> —— 国内 / 国际双端点</li>
    <li><strong>本地</strong> —— Ollama / vLLM / llama.cpp / SGLang 或任意 OpenAI 兼容 server</li>
  </ul>
  <p>设置用 <code>hermes model</code>，或编辑 <code>~/.hermes/.env</code>。全部 key 见 <a data-page="reference/environment-variables">环境变量</a>。</p>

  <h3>Windows 上能跑吗？</h3>
  <p><strong>不原生支持。</strong>Hermes 需要类 Unix 环境。Windows 上装 <a href="https://learn.microsoft.com/en-us/windows/wsl/install" target="_blank" rel="noopener">WSL2</a> 后在 WSL2 里运行。标准 install 命令在 WSL2 里完美工作：</p>
  <pre><code>curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash</code></pre>

  <h3>Android / Termux 上能跑吗？</h3>
  <p>可以 —— Hermes 有已测试的 Termux 路径。一键安装：</p>
  <pre><code>curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash</code></pre>
  <p>完全手动步骤、支持的 extras、当前限制见 <a data-page="getting-started/termux">Termux 指南</a>。</p>
  <p>关键限制：<code>.[all]</code> 在 Android 上<strong>不可用</strong>，因为 <code>voice</code> 依赖 <code>faster-whisper</code> → <code>ctranslate2</code>，后者不发 Android wheel。用 <code>.[termux]</code> 代替。</p>

  <h3>我的数据会被送到哪？</h3>
  <p>API 调用<strong>只发往你配置的 LLM provider</strong>（如 OpenRouter、本地 Ollama）。Hermes 不采集遥测、使用量、分析数据。你的对话、记忆、skill 都存在本地 <code>~/.hermes/</code>。</p>

  <h3>能离线 / 本地模型吗？</h3>
  <p>能。<code>hermes model</code> 选 <strong>Custom endpoint</strong>，填服务器 URL：</p>
  <pre><code>hermes model
# 选：Custom endpoint (enter URL manually)
# API base URL: http://localhost:11434/v1
# API key: ollama
# Model name: qwen3.5:27b
# Context length: 32768   ← 改成与你服务器实际 context 一致</code></pre>
  <p>或直接配 <code>config.yaml</code>：</p>
  <pre><code>model:
  default: qwen3.5:27b
  provider: custom
  base_url: http://localhost:11434/v1</code></pre>
  <p>Hermes 会把 endpoint / provider / base_url 存在 <code>config.yaml</code> 里，重启保留。本地服务器只挂了一个模型时，<code>/model custom</code> 自动识别。<code>provider: custom</code> 是一等 provider，不是别名。</p>

  <h2>故障排查</h2>

  <h3>Hermes 装完，但 CLI 没启动 / 命令找不到</h3>
  <pre><code>source ~/.bashrc
which hermes              # 看路径是否正确
hermes doctor</code></pre>

  <h3>模型回复空 / 错乱</h3>
  <ul>
    <li>provider 认证不对 → <code>hermes model</code> 重选 + 确认 API key</li>
    <li>自定义 endpoint → 先在别的客户端独立验证 endpoint</li>
    <li>模型上下文 &lt; 64K → Hermes 启动时会拒</li>
  </ul>

  <h3>Gateway 启动了但没响应</h3>
  <pre><code>hermes gateway status
hermes logs gateway -f
# 检查 allowlist 或做 DM pairing</code></pre>

  <h3>Cron 不跑</h3>
  <pre><code>hermes cron list
hermes cron show &lt;id&gt;
hermes cron run &lt;id&gt;     # 手动触发
hermes gateway status    # cron 依赖 Gateway 在跑</code></pre>
  <p>更多：<a data-page="guides/cron-troubleshooting">Cron 排错</a>。</p>

  <h3>MCP server 没发现工具</h3>
  <ul>
    <li><code>uv pip install -e ".[mcp]"</code> 装过吗</li>
    <li><code>mcp_servers.&lt;name&gt;.command / url</code> 是否正确</li>
    <li>日志里看 MCP 启动错误</li>
    <li><code>/reload-mcp</code></li>
  </ul>

  <h3>超时 / 429</h3>
  <ul>
    <li>开 <a data-page="user-guide/features/credential-pools">Credential Pools</a>（多 key 轮转）</li>
    <li>开 <a data-page="user-guide/features/fallback-providers">Fallback Providers</a></li>
    <li>调 <code>providers.&lt;id&gt;.request_timeout_seconds</code></li>
  </ul>

  <h3>上下文"满了"</h3>
  <ul>
    <li><code>/compress</code> 手动压缩</li>
    <li><code>/new</code> 开新会话</li>
    <li>降低 <code>compression.threshold</code></li>
    <li>减少 <code>AGENTS.md</code> 体积</li>
  </ul>

  <h2>更多求助</h2>
  <ul>
    <li><code>hermes doctor</code> / <code>hermes dump</code> —— 把输出贴到 issue 或 Discord</li>
    <li>GitHub Issues：<a href="https://github.com/NousResearch/hermes-agent/issues" target="_blank" rel="noopener">NousResearch/hermes-agent/issues</a></li>
    <li>Discord：<a href="https://discord.gg/nousresearch" target="_blank" rel="noopener">Nous Research Discord</a></li>
  </ul>
  <p>本页对照英文 <a href="https://hermes-agent.nousresearch.com/docs/reference/faq/" target="_blank" rel="noopener">FAQ & Troubleshooting</a> 翻译并补充。</p>
</section>
