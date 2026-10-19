---
title: "Copilot、Cursor、Claude Code 约束文件全解"
date: 2026-10-19
categories: [AI]
tags: [GitHub Copilot, Cursor, Claude Code, AGENTS.md, CLAUDE.md, Rules, Skills, AI Agent, 配置]
excerpt: "同一个仓库里经常同时躺着 .github/copilot-instructions.md、.cursor/rules/*.mdc、CLAUDE.md、AGENTS.md、.claude/skills……它们各管什么、作用域多大、在 Linux 和 Windows 上分别放在哪？哪些路径是三个 Agent 都能读到的？本文按「文件类型 → 作用域 → 单个产品 → 三家交集」的顺序一次讲清。"
---

> 用 AI 写代码久了，仓库根目录会越来越热闹：`.github/copilot-instructions.md`、`.cursor/rules/`、`CLAUDE.md`、`AGENTS.md`、`.claude/skills/`……
>
> 常见困惑有三个：**这些文件到底谁读？管多大范围？换台 Windows 电脑要放哪？** 如果团队里有人用 Copilot、有人用 Cursor、有人用 Claude Code，还会多一个问题：**能不能只写一份，三家都认？**
>
> 本文基于三家 2026 年 10 月的官方文档整理。产品更新很快，文末附了原始文档链接，遇到出入以官方为准。

---

## 一、先建立框架：两个维度看所有文件

所有这些 `.md` / `.mdc` 文件，都可以用两个问题定位：**它是哪一类东西？它管多宽？**

### 1.1 按「是什么」分四类

| 类别 | 一句话 | 何时进入模型上下文 | 典型文件 |
| --- | --- | --- | --- |
| **指令（Instructions / Memory）** | 项目的「总纲」：架构、命令、编码规范 | 会话开始时**总是**加载 | `copilot-instructions.md`、`AGENTS.md`、`CLAUDE.md` |
| **规则（Rules / 定向指令）** | 只对某些文件、某类任务生效的规范 | **按条件**加载：匹配 glob、模型判断相关、或手动 @ | `.cursor/rules/*.mdc`、`*.instructions.md`、`.claude/rules/*.md` |
| **技能（Skills）** | 一项具体任务的操作手册，可附带脚本 | **按需**：先只给模型看名字和描述，用到才读全文 | `<目录>/skills/<名字>/SKILL.md` |
| **子代理 / 自定义 Agent** | 一个带独立提示词、可限定工具的「专职助手」 | 被主 Agent 委派或用户选中时 | `.claude/agents/*.md`、`.cursor/agents/*.md`、`*.agent.md` |

还有一类不是给模型「读」的，而是给宿主程序**执行**的：权限、MCP 服务器、Hooks（如 `settings.json`、`mcp.json`、`hooks.json`）。它们不进提示词，但决定 Agent 能碰什么。本文只在需要时顺带提到。

一个容易混的点：**指令和规则是「怎么做事」的规范，不增加能力；技能是「做某件事的流程」，可以带脚本，但脚本也要靠 Agent 自己的终端工具去跑。** 这和我在 [Function Calling / MCP / Skills / Plugin](/2026/07/28/function-calling-mcp-skills/) 那篇里讲的分层一致。

### 1.2 按「管多宽」分五层

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '15px'}}}%%
graph TB
    O["🏢 组织 / 企业层<br/>管理员统一下发，个人通常改不了"] --> U["👤 用户层<br/>~ 家目录，对你所有项目生效，不进 git"]
    U --> P["📁 项目层<br/>仓库根目录，提交到 git，全队共享"]
    P --> S["📂 子目录层<br/>monorepo 某个包里，只管这个目录及以下"]
    P --> L["🔒 本地私有层<br/>项目内但不提交（如 CLAUDE.local.md）"]

    style O fill:#c7ceea,color:#3d3556
    style U fill:#ffdac1,color:#3d3556
    style P fill:#b5ead7,color:#3d3556,stroke:#81c7b5,stroke-width:2px
    style S fill:#e2f0cb,color:#3d3556
    style L fill:#ffd6e0,color:#3d3556
</div>

三家在**合并方式**上有个共同点：多层文件**叠加**进上下文，而不是「下层覆盖上层」。冲突时各家的说法不同（Cursor 写了优先级，VS Code 明确说不要依赖顺序），**最稳妥的做法是别让不同层互相矛盾**。

### 1.3 先说清楚 `~` 在两个系统上是哪

下文所有 `~` 都指当前用户的家目录：

| 写法 | Linux | Windows |
| --- | --- | --- |
| `~` | `/home/<用户名>` | `C:\Users\<用户名>`（即 `%USERPROFILE%`） |
| `~/.claude` | `/home/<用户名>/.claude` | `C:\Users\<用户名>\.claude` |
| `~/.cursor` | `/home/<用户名>/.cursor` | `C:\Users\<用户名>\.cursor` |
| `~/.copilot` | `/home/<用户名>/.copilot` | `C:\Users\<用户名>\.copilot` |

项目内的路径（`.github/...`、`.cursor/...`、`.claude/...`）两个系统完全一样，只是 Windows 资源管理器里显示为反斜杠。**文件内容里引用路径时一律用 `/`**，三家都能正确解析，也方便跨系统共享。

> **WSL 用户注意**：在 WSL 里跑的 Claude Code / Cursor CLI，`~` 是 Linux 家目录 `/home/<用户名>`，**不是** Windows 的 `C:\Users\<用户名>`。两边的用户级配置互不相通。

---

## 二、GitHub Copilot（以 VS Code 为主）

VS Code 现在把「谁来跑这个 Agent」叫做 **harness**：Copilot、Anthropic Claude、OpenAI Codex、Local 等。**选不同的 harness，能读到的文件不一样。** 下面以默认的 Copilot harness 为主，Local harness 的差异单独标出。

### 2.1 文件一览

| 文件 | 类别 | 作用域 | 何时生效 |
| --- | --- | --- | --- |
| `.github/copilot-instructions.md` | 指令 | 项目 | 每次对话自动加载。github.com 上的 Copilot 代码评审、Coding Agent 也会读 |
| `AGENTS.md`（根目录） | 指令 | 项目 | 每次对话自动加载。子目录嵌套 `AGENTS.md` 目前是**实验功能** |
| `.github/instructions/**/*.instructions.md` | 规则 | 项目（可按 glob 缩到子目录） | `applyTo` 匹配到 Agent 正在创建/修改的文件时自动附加；只写 `description` 则由模型判断；都不写就只能手动附加 |
| `~/.copilot/copilot-instructions.md` | 指令 | 用户 | 你个人的、所有项目都生效的总纲 |
| `~/.copilot/instructions/*.instructions.md` | 规则 | 用户 | 同上，按 `applyTo` / `description` 生效 |
| 组织级自定义指令 | 指令 | 组织 | 在 GitHub 组织设置里配，需开启 `github.copilot.chat.organizationInstructions.enabled` |
| `.github/skills/<名字>/SKILL.md` | 技能 | 项目 | 按需加载 |
| `.github/agents/*.agent.md` | 自定义 Agent | 项目 | 在 Agent 下拉里选中后，正文前置到提示词 |
| `~/.copilot/agents/*.agent.md` | 自定义 Agent | 用户 | 同上 |
| `.github/prompts/*.prompt.md` | 提示词模板 | 项目 | 用 `/名字` 手动触发。**仅 Local harness 支持**，Copilot harness 建议改写成 skill |
| `.vscode/mcp.json` | MCP 配置 | 项目 | 宿主读取，不进提示词 |

`.instructions.md` 的头部长这样：

```markdown
---
name: 'Python testing'
description: 'Use when creating or updating Python unit tests.'
applyTo: '**/*.py'
---
- 用 tests/conftest.py 里的 fixture，不要重复写 setup
- 改完测试后运行 python -m pytest <测试文件>
```

**Copilot 还能读别家的格式**（这是第五节「共同路径」的来源）：

| 别家格式 | Copilot 能否读 | 条件 |
| --- | --- | --- |
| `.claude/skills/`、`~/.claude/skills/` | ✅ | 官方列为项目/个人技能目录 |
| `.agents/skills/`、`~/.agents/skills/` | ✅ | 同上 |
| `.claude/agents/*.md` | ✅ | 按 Claude 子代理格式识别，工具名会自动映射 |
| `CLAUDE.md`、`.claude/CLAUDE.md`、`~/.claude/CLAUDE.md`、`CLAUDE.local.md` | ⚠️ | Claude harness 原生读；Local harness 需开 `chat.useClaudeMdFile`。**Copilot harness 推荐的仍是 `copilot-instructions.md` / `AGENTS.md`** |
| `.claude/rules/*.md` | ⚠️ | Claude harness 和 Local harness 支持，用 `paths` 而不是 `applyTo` |

### 2.2 Linux / Windows 路径

| 内容 | Linux | Windows |
| --- | --- | --- |
| 用户总纲 | `~/.copilot/copilot-instructions.md` | `%USERPROFILE%\.copilot\copilot-instructions.md` |
| 用户规则 | `~/.copilot/instructions/` | `%USERPROFILE%\.copilot\instructions\` |
| 用户技能 | `~/.copilot/skills/` | `%USERPROFILE%\.copilot\skills\` |
| 用户自定义 Agent | `~/.copilot/agents/` | `%USERPROFILE%\.copilot\agents\` |
| VS Code 用户配置（settings.json、Local harness 的用户提示词等） | `~/.config/Code/User/` | `%APPDATA%\Code\User\` |

两个容易踩的点：

- **`~/.copilot/` 不走 VS Code 的 Settings Sync。** 换电脑要自己拷。只有存在 VS Code 用户配置目录里的 Local harness 指令才能同步。
- 以前存在 VS Code 用户配置目录里的 Agent、指令，**Copilot harness 不读**。Agent Customizations 编辑器里如果出现「Migrations」，就是在提醒你迁到 `~/.copilot/`。

### 2.3 默认能访问的目录

| 能力 | 默认范围 |
| --- | --- |
| 内置读写文件工具 | **只限当前打开的工作区文件夹** |
| 额外只读目录 | 用 `chat.additionalReadAccessFolders` 授权，只读不可写 |
| 编辑敏感文件 | 系统目录、dotfile、工作区外的文件默认要你确认；可用 `chat.tools.edits.autoApprove` 加规则，例如 `"**/.env": false` |
| 终端命令 | 以你的用户身份运行，**能碰到你账号能碰的一切**；开启 Agent 沙箱后默认只能读写当前工作目录 |
| monorepo 父目录配置 | 默认只扫打开的文件夹；开 `chat.useCustomizationsInParentRepositories` 后会一路向上找到 `.git` 所在的仓库根 |

---

## 三、Cursor

### 3.1 文件一览

| 文件 / 位置 | 类别 | 作用域 | 何时生效 |
| --- | --- | --- | --- |
| `.cursor/rules/**/*.mdc` | 规则 | 项目（可放子文件夹分类） | 由头部三个字段决定，见下表 |
| `AGENTS.md`（根目录） | 指令 | 项目 | 每次对话加载 |
| `AGENTS.md`（任意子目录） | 指令 | 该目录及以下 | Agent 处理该目录里的文件时加载，和上级叠加，**越具体越优先** |
| `CLAUDE.md`（根目录） | 指令 | 项目 | 官方 CLI 文档写明会读，和 `AGENTS.md` 一起当规则生效；总是加载 |
| User Rules | 指令 | 用户（跟账号走） | 在 Customize → Rules 里填，每次对话加载 |
| Team Rules | 规则 | 团队所有仓库 | 管理后台配置，可强制开启；支持 glob |
| `.cursorrules` | 指令 | 项目 | **旧格式，已弃用**，新项目别用 |
| `.cursor/skills/`、`.agents/skills/` | 技能 | 项目；放在子目录里则只管该子目录 | 按需加载 |
| `~/.cursor/skills/`、`~/.agents/skills/` | 技能 | 用户（本机） | 按需加载 |
| `.cursor/agents/*.md` | 子代理 | 项目 | 主 Agent 委派时 |
| `~/.cursor/agents/*.md` | 子代理 | 用户 | 同上 |
| `.cursor/mcp.json` / `~/.cursor/mcp.json` | MCP 配置 | 项目 / 用户 | 宿主读取 |

`.mdc` 规则的四种生效方式，全看头部三个字段怎么组合：

| `alwaysApply` | `description` | `globs` | 行为 |
| --- | --- | --- | --- |
| `true` | — | — | 每次对话都带上 |
| `false` | — | 有 | 上下文里出现匹配文件时自动附加 |
| `false` | 有 | 无 | 模型看描述，自己决定要不要拉进来 |
| `false` | 无 | 无 | 只有在对话里 `@规则名` 才生效 |

```markdown
---
description: React 组件规范
globs: src/components/**/*.tsx
alwaysApply: false
---
- 用具名导出，不用 default export
- 单个组件不超过 200 行，超了就拆
```

**三个 Cursor 特有的坑：**

1. `.cursor/rules/` 里的**纯 `.md` 文件会被忽略**，必须是 `.mdc`。想写纯 Markdown 就用 `AGENTS.md`。
2. 规则**只作用于 Agent（Chat）**，不影响 Tab 补全，User Rules 也不作用于 Ctrl+K 行内编辑。
3. 规则里写 `@某文件` 只是告诉 Agent 去哪读，**不会把文件内容内联进提示词**。必须常驻的内容要直接写进规则正文。

冲突时的优先级：**Team Rules → 项目规则 → User Rules**，前者优先。

**Cursor 是三家里「兼容别家」最积极的：**

| 别家目录 | Cursor 是否读取 |
| --- | --- |
| `.claude/skills/`、`~/.claude/skills/` | ✅ 官方兼容 |
| `.codex/skills/`、`~/.codex/skills/` | ✅ 官方兼容 |
| `.claude/agents/`、`~/.claude/agents/` | ✅ 子代理兼容（同名时 `.cursor/` 优先） |
| `.codex/agents/`、`~/.codex/agents/` | ✅ 子代理兼容 |
| `CLAUDE.md`（根目录） | ✅ |
| `.claude/rules/`、`~/.claude/CLAUDE.md` | ❌ 官方文档未提及 |
| `.github/copilot-instructions.md`、`.github/instructions/` | ❌ |

### 3.2 Linux / Windows 路径

| 内容 | Linux | Windows |
| --- | --- | --- |
| 用户技能 | `~/.cursor/skills/` | `%USERPROFILE%\.cursor\skills\` |
| 用户子代理 | `~/.cursor/agents/` | `%USERPROFILE%\.cursor\agents\` |
| 用户 MCP | `~/.cursor/mcp.json` | `%USERPROFILE%\.cursor\mcp.json` |
| CLI 权限配置 | `~/.cursor/cli-config.json` | `%USERPROFILE%\.cursor\cli-config.json` |
| 编辑器 settings.json | `~/.config/Cursor/User/` | `%APPDATA%\Cursor\User\` |
| User Rules | 存在账号里，不落本地文件 | 同左 |

`~/.cursor/skills-cursor/` 是 Cursor 自带技能的目录，由系统自动管理，**不要往里放自己的技能**。

### 3.3 默认能访问的目录

- **以打开的工作区为边界。** Agent 的文件读写、代码搜索都围绕当前打开的文件夹（多根工作区则是每个根）。
- **终端命令以你的用户身份运行。** 是否自动执行、是否进沙箱，由设置里的自动运行 / 沙箱选项决定。
- **云端 Agent 读不到你本机的用户级技能。** 只有 `~/.cursor/skills/` 能通过「Sync Skills for Cloud Agents」同步上去；`~/.agents/skills/` 和项目外的东西都不会带走。要在云端或自建 worker 上用，就把技能提交到仓库里。

---

## 四、Claude Code

### 4.1 文件一览

| 文件 | 类别 | 作用域 | 何时生效 |
| --- | --- | --- | --- |
| 托管 `CLAUDE.md` | 指令 | 组织（整台机器） | IT 下发，总是加载 |
| `~/.claude/CLAUDE.md` | 指令 | 用户 | 总是加载 |
| `./CLAUDE.md` 或 `./.claude/CLAUDE.md` | 指令 | 项目 | 启动时加载。**从当前目录一路向上**的每一级都会加载 |
| `./CLAUDE.local.md` | 指令 | 本地私有 | 同级 `CLAUDE.md` 之后追加；自己加进 `.gitignore` |
| 子目录里的 `CLAUDE.md` | 指令 | 该子目录 | **启动时不加载**，Claude 读写那个目录里的文件时才加载 |
| `AGENTS.md` | 指令 | 项目 / 子目录 | **v2.1.277 起原生支持**，但默认只在**没有任何 `CLAUDE.md` / `CLAUDE.local.md`** 时才读，详见下方 |
| `.claude/rules/**/*.md` | 规则 | 项目 | 没写 `paths` 就总是加载；写了 `paths` 就只在处理匹配文件时加载 |
| `~/.claude/rules/*.md` | 规则 | 用户 | 同上，所有项目生效 |
| `.claude/skills/<名字>/SKILL.md` | 技能 | 项目（从当前目录向上到仓库根都扫） | 按需加载，或 `/名字` 手动触发 |
| `~/.claude/skills/<名字>/SKILL.md` | 技能 | 用户 | 同上 |
| `.claude/commands/*.md` | 斜杠命令 | 项目 / 用户 | 已并入技能机制，旧文件照常可用 |
| `.claude/agents/*.md` / `~/.claude/agents/*.md` | 子代理 | 项目 / 用户 | 主 Agent 委派时 |
| `.claude/settings.json` | 权限 / Hooks / 环境变量 | 项目（提交） | 宿主执行，不进提示词 |
| `.claude/settings.local.json` | 同上 | 本地私有 | 不提交 |
| `.mcp.json`（根目录） | MCP 配置 | 项目 | 宿主读取 |
| `~/.claude/projects/<项目>/memory/` | 自动记忆 | 用户 × 项目 | Claude 自己记的笔记，`MEMORY.md` 索引每次加载 |

几个 Claude Code 独有的机制：

- **`@路径` 导入**：`CLAUDE.md` 里写 `@docs/architecture.md`，启动时就把那份文件展开进上下文，最多递归四层。项目文件导入仓库外的路径，第一次会弹窗让你确认。
- **规则用 `paths`，不是 `globs` / `applyTo`**：

```markdown
---
paths:
  - "src/api/**/*.ts"
---
- 所有接口必须先做参数校验
```

- **加载顺序是「从根到叶」拼接**：在 `foo/bar/` 启动时，`foo/CLAUDE.md` 在前，`foo/bar/CLAUDE.md` 在后，离你越近的越晚读到。

**`AGENTS.md` 什么时候被读（默认设置下）：**

| 仓库里有什么 | Claude 读什么 |
| --- | --- |
| 只有 `AGENTS.md` | `AGENTS.md` |
| `AGENTS.md` + 任意一个 `CLAUDE.md` / `CLAUDE.local.md` | **只读 `CLAUDE.md`**，`AGENTS.md` 被跳过 |
| `CLAUDE.md` 里写了 `@AGENTS.md` | 两者都读，`AGENTS.md` 通过导入进来 |

注意：你家目录的 `~/.claude/CLAUDE.md`、托管 `CLAUDE.md`、`.claude/rules/` **不算**在这个判断里，不会挡住 `AGENTS.md`。想两者并读，可在 `/config` 里把 Project instructions 设为 `claude-md-and-agents-md`。

### 4.2 Linux / Windows 路径

| 内容 | Linux（含 WSL） | Windows |
| --- | --- | --- |
| 托管 `CLAUDE.md` | `/etc/claude-code/CLAUDE.md` | `C:\Program Files\ClaudeCode\CLAUDE.md` |
| 托管设置 | `/etc/claude-code/managed-settings.json` | `C:\Program Files\ClaudeCode\managed-settings.json`（或注册表 `HKLM\SOFTWARE\Policies\ClaudeCode`） |
| 企业级技能 | `/etc/claude-code/.claude/skills/` | `C:\Program Files\ClaudeCode\.claude\skills\` |
| 用户目录 | `~/.claude/` | `%USERPROFILE%\.claude\` |
| 用户总纲 | `~/.claude/CLAUDE.md` | `%USERPROFILE%\.claude\CLAUDE.md` |
| 用户规则 / 技能 / 子代理 | `~/.claude/rules/`、`skills/`、`agents/` | `%USERPROFILE%\.claude\rules\`、`skills\`、`agents\` |
| 用户设置 | `~/.claude/settings.json` | `%USERPROFILE%\.claude\settings.json` |
| 全局状态（登录、个人 MCP） | `~/.claude.json` | `%USERPROFILE%\.claude.json` |

- 设置了环境变量 `CLAUDE_CONFIG_DIR` 后，上面所有 `~/.claude` 都会换到那个目录下。
- Windows 上**不要用旧路径** `C:\ProgramData\ClaudeCode\managed-settings.json`，新版已经不读了。
- Windows 上**别用符号链接**把 `CLAUDE.md` 指向 `AGENTS.md`：建链接要管理员权限或开发者模式，Git 默认还会把它检出成一行文本。用 `@AGENTS.md` 导入更稳。

### 4.3 默认能访问的目录

| 能力 | 默认范围 |
| --- | --- |
| 文件工具（Read / Edit / Write） | **启动 `claude` 的那个目录及其子目录**。默认模式下，读写这之外的路径会弹窗确认 |
| 扩展工作目录 | 启动时 `--add-dir <路径>`、会话里 `/add-dir`、或在 `settings.json` 的 `permissions.additionalDirectories` 里写 |
| Bash 命令 | 你批准后能做你账号能做的一切。开 `/sandbox` 后：**写**限于工作目录、临时目录和额外目录；**读**默认仍能读大部分机器（包括 `~/.ssh`），要用 `denyRead` 自己挡 |
| 收紧读取 | `permissions.blockReadsOutsideWorkingDirectories: true` 让文件工具在任何模式下都拒绝读工作目录外的文件 |

**一个反直觉的细节：「能访问」不等于「会加载配置」。**

| 额外目录的添加方式 | 能读写文件 | 加载该目录的 `CLAUDE.md` / rules | 加载该目录的 skills |
| --- | --- | --- | --- |
| `permissions.additionalDirectories` | ✅ | ❌ | ❌ |
| `--add-dir` / `/add-dir` | ✅ | 仅当设置 `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1` | ✅ |

另外，**从哪个目录启动很重要**：在 `packages/api/` 里启动，Claude 默认改不了隔壁的 `packages/web/`；但仓库根的 `CLAUDE.md` 和 `.claude/skills/` 仍会因为「向上查找」被加载。

---

## 五、重点：三个 Agent 都能读到的共同路径

把前三节的兼容表叠在一起，就得到下面这张总表。**✅ = 官方明确支持，⚠️ = 有条件，❌ = 不读。**

### 5.1 总表

| 路径 | Copilot | Cursor | Claude Code | 三家都认？ |
| --- | :---: | :---: | :---: | :---: |
| **`AGENTS.md`（根目录）** | ✅ | ✅ | ✅ 无 `CLAUDE.md` 时 | **✅ 有条件** |
| `AGENTS.md`（子目录嵌套） | ⚠️ 实验 | ✅ | ✅ 无同级 `CLAUDE.md` 时 | ⚠️ |
| `CLAUDE.md`（根目录） | ⚠️ 看 harness | ✅ | ✅ | ⚠️ |
| `~/.claude/CLAUDE.md` | ⚠️ 看 harness | ❌ | ✅ | ❌ |
| `.claude/rules/*.md` | ⚠️ 看 harness | ❌ | ✅ | ❌ |
| **`.claude/skills/`** | ✅ | ✅ | ✅ | **✅** |
| **`~/.claude/skills/`** | ✅ | ✅ | ✅ | **✅** |
| `.agents/skills/`、`~/.agents/skills/` | ✅ | ✅ | ❌ | ❌ |
| **`.claude/agents/`** | ✅ | ✅ | ✅ | **✅** |
| `~/.claude/agents/` | ⚠️ Claude harness | ✅ | ✅ | ⚠️ |
| `.github/copilot-instructions.md` | ✅ | ❌ | ❌ | ❌ |
| `.github/instructions/`、`.github/skills/` | ✅ | ❌ | ❌ | ❌ |
| `.cursor/rules/`、`.cursor/skills/` | ❌ | ✅ | ❌ | ❌ |
| MCP 配置 | `.vscode/mcp.json` | `.cursor/mcp.json` | `.mcp.json` | ❌ 各写各的 |

### 5.2 读懂这张表

**能三家通用的只有三样：**

1. **项目总纲：根目录 `AGENTS.md`**——前提是**仓库里不要再放 `CLAUDE.md`**，否则 Claude Code 默认会跳过 `AGENTS.md`。
2. **技能：`.claude/skills/`（项目）和 `~/.claude/skills/`（个人）**——这是目前唯一三家都官方支持的技能目录。注意不是 `.agents/skills/`，Claude Code 不扫那里。
3. **子代理：`.claude/agents/`**——三家都识别，但各家支持的头部字段不同（Claude Code 最丰富，Cursor 只认 `name`、`description`、`model`、`readonly`、`is_background`），用最小公共字段写最保险。

**没有三家通用的东西：**

- **按文件类型生效的规则（glob 规则）**。Cursor 用 `.cursor/rules/*.mdc` + `globs`，Copilot 用 `*.instructions.md` + `applyTo`，Claude Code 用 `.claude/rules/*.md` + `paths`。语法和目录都不同，只能各写一份，或者干脆放进 `AGENTS.md` 用文字说明「改 `src/api/` 时要……」。
- **用户级总纲**。`~/.copilot/copilot-instructions.md`、Cursor 账号里的 User Rules、`~/.claude/CLAUDE.md` 三处互不相通。
- **MCP 配置和权限**。文件名、字段都不同。

### 5.3 一图看交集

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '14px'}}}%%
graph TB
    subgraph Shared["✅ 三家共同可读"]
        A1["AGENTS.md（根目录）<br/>⚠️ 别同时放 CLAUDE.md"]
        A2[".claude/skills/<br/>~/.claude/skills/"]
        A3[".claude/agents/"]
    end

    subgraph CP["只有 Copilot"]
        C1[".github/copilot-instructions.md"]
        C2[".github/instructions/*.instructions.md"]
        C3["~/.copilot/"]
    end

    subgraph CU["只有 Cursor"]
        U1[".cursor/rules/*.mdc"]
        U2["User Rules / Team Rules"]
        U3["~/.cursor/"]
    end

    subgraph CC["只有 Claude Code"]
        K1["CLAUDE.local.md"]
        K2["~/.claude/CLAUDE.md<br/>~/.claude/rules/"]
        K3[".claude/settings.json"]
    end

    style Shared fill:#b5ead7,color:#3d3556,stroke:#81c7b5,stroke-width:2px
    style CP fill:#c7ceea,color:#3d3556
    style CU fill:#ffdac1,color:#3d3556
    style CC fill:#ffd6e0,color:#3d3556
</div>

---

## 六、推荐的仓库布局

如果团队三家混用，下面这套布局能做到「通用内容只写一份，特有能力各自补充」：

```text
my-repo/
├── AGENTS.md                         # ✅ 三家共读：架构、构建/测试命令、通用规范
├── .claude/
│   ├── skills/                       # ✅ 三家共读：团队技能
│   │   └── release-checklist/
│   │       └── SKILL.md
│   ├── agents/                       # ✅ 三家共读：子代理（只用 name/description 等公共字段）
│   │   └── code-reviewer.md
│   ├── rules/                        # Claude Code（及 VS Code 的 Claude harness）：按路径生效的规则
│   │   └── api.md
│   └── settings.json                 # Claude Code：权限、Hooks
├── .cursor/
│   └── rules/                        # Cursor：按 glob 生效的规则
│       └── api.mdc
├── .github/
│   └── instructions/                 # Copilot：按 applyTo 生效的规则
│       └── api.instructions.md
└── .gitignore                        # 加上 CLAUDE.local.md、.claude/settings.local.json
```

配套的几条原则：

1. **`AGENTS.md` 是唯一真源。** 不要再建 `CLAUDE.md` 和 `.github/copilot-instructions.md` 去复制同样的内容。三份文件早晚会改得不一致。
2. **如果某些同事的 Claude Code 版本低于 v2.1.277**，可以加一个只有一行 `@AGENTS.md` 的 `CLAUDE.md`。但要知道 Cursor 也会读根目录 `CLAUDE.md`，这一行导入在 Cursor 里只是一句无害的文字，不会重复加载正文。
3. **glob 规则三家各写一份时，内容保持一致。** 最好让一份是源头（比如 `.claude/rules/api.md`），另外两份在开头注明「与 `.claude/rules/api.md` 保持同步」。
4. **个人偏好放用户层，不进仓库。** 回复语言、啰嗦程度这类东西，分别写进 `~/.copilot/copilot-instructions.md`、Cursor 的 User Rules、`~/.claude/CLAUDE.md`。
5. **个人技能放 `~/.claude/skills/`。** 这是唯一三家都会扫的用户级技能目录，写一次三个工具都能用。

---

## 七、常见问题速查

| 现象 | 原因 | 解决 |
| --- | --- | --- |
| Cursor 不认我的规则 | `.cursor/rules/` 里放的是 `.md` | 改成 `.mdc`，或改写进 `AGENTS.md` |
| Claude Code 不读 `AGENTS.md` | 仓库里还有 `CLAUDE.md` 或 `CLAUDE.local.md`；或版本低于 v2.1.277 | 删掉 `CLAUDE.md`，或在里面写 `@AGENTS.md` |
| 加了 `CLAUDE.local.md` 后 `AGENTS.md` 失效了 | `CLAUDE.local.md` 也算「有 `CLAUDE.md`」 | `/config` 里设 `claude-md-and-agents-md` |
| Copilot 不读 `CLAUDE.md` | 当前用的是 Copilot harness | 换 `AGENTS.md`，或切到 Claude / Local harness |
| `.agents/skills/` 在 Claude Code 里不出现 | Claude Code 不扫这个目录 | 改放 `.claude/skills/` |
| 子目录的 `CLAUDE.md` 没生效 | Claude Code 子目录指令是**懒加载**，碰到那个目录的文件才读 | 正常现象；要立即生效就从那个目录启动 |
| 换电脑后 Copilot 个人指令没了 | `~/.copilot/` 不走 Settings Sync | 手动拷贝，或放进 dotfiles 仓库 |
| Cursor 云端 Agent 找不到我的技能 | 只有 `~/.cursor/skills/` 可同步，且要手动开启 | 开 Sync Skills，或把技能提交到仓库 |
| Windows 上 `CLAUDE.md` 变成一行路径 | 符号链接被 Git 检出成普通文本 | 改用 `@AGENTS.md` 导入 |

---

## 八、一句话总结

> **指令写进 `AGENTS.md`，技能放进 `.claude/skills/`，子代理放进 `.claude/agents/`——这三处是 Copilot、Cursor、Claude Code 的公共地盘；按文件类型生效的规则、个人偏好、MCP 和权限，至今还得各家各写。**

---

## 参考文档

- GitHub Copilot / VS Code：[Custom instructions](https://code.visualstudio.com/docs/agent-customization/custom-instructions)、[Agent Skills](https://code.visualstudio.com/docs/agent-customization/agent-skills)、[Custom agents](https://code.visualstudio.com/docs/agent-customization/custom-agents)、[Prompt files](https://code.visualstudio.com/docs/agent-customization/prompt-files)、[Customizations overview](https://code.visualstudio.com/docs/agent-customization/overview)、[Security](https://code.visualstudio.com/docs/agents/run/security)
- Cursor：[Rules](https://cursor.com/docs/rules)、[Agent Skills](https://cursor.com/docs/skills)、[Subagents](https://cursor.com/docs/subagents)、[CLI](https://cursor.com/docs/cli/using)
- Claude Code：[Memory / CLAUDE.md](https://code.claude.com/docs/en/memory)、[.claude 目录](https://code.claude.com/docs/en/claude-directory)、[Skills](https://code.claude.com/docs/en/skills)、[Permissions](https://code.claude.com/docs/en/permissions)、[Sandboxing](https://code.claude.com/docs/en/sandboxing)、[Managed settings](https://code.claude.com/docs/en/managed-settings)

---

*本文写于 2026 年 10 月 19 日，基于三家当月官方文档。*
