---
title: "多个 Agent 怎么一起干活？协作交互一次讲明白"
date: 2026-07-31
categories: [AI]
tags: [AI Agent, 多智能体, Multi-Agent, CrewAI, LangGraph, AutoGen, MCP, A2A, 协作]
excerpt: "从「一个人干所有活」到「一个小团队分工」，讲清多 Agent 协作的本质、五种常见组织方式、它们如何传消息/共享状态/交接任务，并用「周末出游计划」走完一轮完整交互；文末附 GitHub 开源框架索引，方便对照上手。"
---

> 上一篇讲单个 Agent 怎么用工具：[《Function Calling、MCP、Skills》]({{ '/2026/07/28/function-calling-mcp-skills/' | relative_url }})；更早一篇讲 Agent 从哪来：[《AI Agent》]({{ '/2026/04/15/ai-agent/' | relative_url }})。
>
> 这篇只回答一个问题：**多个 Agent 之间到底怎么协作、怎么交互？**  
> 面向小白，少术语、多类比；最后用一个完整例子走一遍，并给出可点开的 GitHub 开源参考。

---

## 1. 先忘掉框架：用「公司小组」理解多智能体

### 1.1 一个 Agent 像什么？

一个 **Agent（智能体）** 可以粗暴理解成：

> **会思考的员工** = 大模型（脑子）+ 工具（手）+ 记忆（笔记本）+ 目标（任务单）

他能：读你的需求 → 决定下一步 → 调工具（搜索、写文件、查数据库）→ 看结果 → 再决定，直到交差。

相关能力分层见：[Function Calling / MCP / Skills]({{ '/2026/07/28/function-calling-mcp-skills/' | relative_url }})。

### 1.2 多个 Agent 又像什么？

多个 Agent = **一个小团队**，而不是「同一个员工复制粘贴成五份」。

| 角色（人） | 对应 Agent | 他擅长什么 |
| --- | --- | --- |
| 项目经理 | 编排 / Supervisor Agent | 拆任务、分活、验收 |
| 调研员 | Research Agent | 上网搜、整理事实 |
| 写手 | Writer Agent | 把材料写成可读文章 |
| 质检 | Critic / Reviewer Agent | 挑错、查遗漏、提修改意见 |
| 专员 | 带特定工具的 Specialist | 只会订票 / 只会画图 / 只会跑代码 |

**为什么要拆？** 因为让一个超级员工「又搜又写又审又订票」，提示词又臭又长，还容易顾此失彼。拆开后：每人目标清晰、工具更少、出错更好查。

### 1.3 一句话本质

> **多 Agent 协作 = 约定好「谁说话、谁动手、产物怎么交接、什么时候算完」。**

框架（CrewAI、LangGraph…）只是把这套约定写成代码，方便你复用。

---

## 2. Agent 之间到底在「交互」什么？

小白最容易误会：以为多个 Agent 会像微信群一样自由闲聊。  
真实系统里，交互通常很「规矩」，常见就下面几类。

### 2.1 四种交互载体

| 载体 | 像什么 | 典型内容 |
| --- | --- | --- |
| **消息（Message）** | 同事发的邮件/工单 | 「请调研上海周末天气」「初稿如下……」 |
| **共享状态（Shared State）** | 团队共用的白板 / 共享文档 | `destination=上海`、`draft=...`、`status=reviewing` |
| **交接（Handoff）** | 「这单我做不了，转给你」 | A 把自己对话权交给 B，B 继续跟用户聊 |
| **工具结果（Tool Result）** | 跑腿回来的回执 | 搜索 API 返回的 JSON、订票成功单号 |

<div class="mermaid">
flowchart LR
    A["Agent A"] -->|"① 消息 / 任务单"| B["Agent B"]
    B -->|"② 写回共享白板"| S["Shared State"]
    A -->|"③ 读白板"| S
    B -->|"④ 调用工具"| T["搜索 / 日历 / 订票 API"]
    T -->|"⑤ 结果"| B
    B -->|"⑥ Handoff"| C["Agent C"]

    style S fill:#ffdac1,color:#3d3556
    style T fill:#bee3db,color:#3d3556
</div>

### 2.2 「说话」和「干活」要分开看

- **对话式协作**：Agent 们轮流发言（像会议），常见于 AutoGen / 群聊模式。  
- **流水线协作**：A 的输出文件直接变成 B 的输入（像产线），常见于 CrewAI 顺序任务。  
- **图状态机协作**：每一步是图上的一个节点，边决定下一步去哪，常见于 LangGraph。

三种都可以，选哪种取决于你要「像开会」还是「像流水线」还是「像可回滚的流程图」。

### 2.3 和 MCP、A2A 的关系（别混）

| 名字 | 解决什么 | 和多 Agent 的关系 |
| --- | --- | --- |
| **MCP** | Agent **怎么连工具/数据** | 多 Agent 可以各自挂不同 MCP 工具 |
| **A2A**（Agent-to-Agent） | **不同系统里的 Agent 怎么互相发现、派活** | 跨框架、跨厂商协作时用 |
| 框架内消息/状态 | **同一应用里**多个 Agent 怎么编排 | 多数教程先学这个 |

先把「一个程序里的多 Agent」搞懂，再考虑跨系统的 A2A。

---

## 3. 五种最常见的协作组织方式

### 3.1 流水线（Pipeline / Sequential）

**像**：传菜口——洗菜 → 切菜 → 炒菜 → 装盘。

```text
研究员 Agent → 写手 Agent → 审稿 Agent → 输出终稿
```

- 优点：好懂、好测、责任清晰  
- 缺点：前面错了，后面全跟着错；缺少灵活回环  

适合：写报告、做翻译润色、固定步骤的办公流程。

### 3.2 主管分派（Supervisor / Manager）

**像**：项目经理接需求，再派给组员，组员交回后再派下一项。

<div class="mermaid">
flowchart TB
    U["用户需求"] --> M["主管 Agent"]
    M --> R["调研 Agent"]
    M --> W["写作 Agent"]
    M --> C["质检 Agent"]
    R --> M
    W --> M
    C --> M
    M --> OUT["最终答复用户"]

    style M fill:#ffdac1,color:#3d3556
</div>

- 优点：灵活，可按中间结果改派  
- 缺点：主管变成瓶颈；提示词要教他「何时停」  

适合：任务步骤不固定、需要中途决策的场景。

### 3.3 辩论 / 互评（Debate / Critique）

**像**：两个人互相挑刺，第三人（或主管）综合。

```text
提案 Agent 提出方案
  ↕ 多轮
反对/质检 Agent 找漏洞
  ↓
汇总 Agent 出终版
```

- 优点：明显提高正确率与严谨性  
- 缺点：费 token、可能抬杠到停不下来（要设轮数上限）  

适合：方案评审、代码审查、事实核查。

### 3.4 交接（Handoff）

**像**：客服中心——售前搞定不了，一键转售后专家，**用户还在同一个对话窗口**。

- Agent A 发现「这是退款问题」→ handoff 给退款 Agent  
- 之后由退款 Agent 继续回复用户  

适合：客服路由、多技能机器人、OpenAI Agents SDK 一类「轻量交接」设计。

### 3.5 层级外包（Hierarchical / Sub-agents）

**像**：总监 → 经理 → 专员；上层只看摘要，下层跑细节。

- 主 Agent 负责规划与验收  
- 子 Agent 被当成「一次性外包小组」拉起来干活，干完把结果交回  

适合：复杂长任务（研究、编码、多步运维）。和 [AI Harness]({{ '/2026/07/30/ai-harness/' | relative_url }}) 里「主循环 + 子任务」的思路很像。

---

## 4. 协作时必须约定的「交通规则」

没有规则的多 Agent，会变成：**互相抄作业、重复劳动、永远说「我再想想」**。

| 规则 | 为什么重要 | 落地做法 |
| --- | --- | --- |
| **角色边界** | 避免人人都想当写手 | 每人一条清晰 Goal / System Prompt |
| **输入输出格式** | 交接不丢信息 | 约定 JSON / Markdown 标题结构 |
| **停止条件** | 防止死循环开会 | 最大轮数、质检分数阈值、人工确认点 |
| **工具权限** | 安全与成本 | 调研员能搜网，写手不能乱删库 |
| **单一真相源** | 避免各说各话 | 共享 State / 共享文档，而不是各记各的 |
| **失败怎么退** | 生产可用 | 重试、回退上一节点、升级给人类 |

---

## 5. 实际例子：三个 Agent 规划「上海周末一日游」

下面这个例子不绑死某一家框架，你用 CrewAI、LangGraph 或自己写 if-else，结构都一样。  
目标：**给用户一份可执行的一日游计划**（含天气、路线、预算提醒）。

### 5.1 角色分工

| Agent | 名字 | 职责 | 可用工具（举例） |
| --- | --- | --- | --- |
| A | 调研员 Research | 查天气、热门景点、地铁信息 | 天气 API、网页搜索 |
| B | 规划师 Planner | 排出时间表与动线 | 地图/距离估算（或纯推理） |
| C | 管家 Critic | 检查是否不合理（下雨还去户外？路程过长？） | 无（只审稿） |

组织方式：**流水线 + 一次打回**（质检不通过就退回规划师改一版）。

<div class="mermaid">
flowchart LR
    U["用户：周六上海一日游<br/>喜欢博物馆，预算中等"] --> A["① 调研员"]
    A -->|"调研笔记"| B["② 规划师"]
    B -->|"行程初稿"| C["③ 管家质检"]
    C -->|"不通过 + 修改意见"| B
    C -->|"通过"| OUT["最终行程发给用户"]

    style A fill:#b5ead7,color:#3d3556
    style B fill:#bee3db,color:#3d3556
    style C fill:#ffdac1,color:#3d3556
</div>

### 5.2 共享白板上有什么？（Shared State）

可以想象成一张 JSON 卡片，大家都能读，按规定字段写：

```json
{
  "user_request": "周六上海一日游，喜欢博物馆，预算中等，尽量少走路",
  "research_notes": null,
  "itinerary_draft": null,
  "review": { "passed": false, "comments": [] },
  "final_plan": null
}
```

### 5.3 第 1 步：调研员干活

**主管/编排器**把任务发给调研员：

> 任务：根据 `user_request`，产出 `research_notes`。必须包含：当日天气、2–3 个博物馆选项、大致票价与开放时间、交通注意点。

调研员内部循环（和单 Agent 一样）：

1. 调用天气工具 → 「周六多云转阴，下午可能小雨」  
2. 调用搜索 → 「上海博物馆、自然博物馆……」  
3. 整理成结构化笔记，**写回白板** `research_notes`

他**不写完整行程**，写了也算越权（角色边界）。

### 5.4 第 2 步：规划师根据笔记排程

编排器读到 `research_notes` 已就绪 → 唤醒规划师：

> 任务：只根据 `research_notes` 生成 `itinerary_draft`（上午/下午/晚上，含交通与备选室内方案）。

规划师输出例如：

```text
09:30 地铁到人民广场
10:00-12:00 上海博物馆
12:30 附近简餐
14:00-16:00 自然博物馆
……
备选：若下雨，下午改为室内商场 + 书店
```

写入 `itinerary_draft`。

### 5.5 第 3 步：管家质检（可能打回）

管家只做检查，例如规则：

- 若下午有雨，是否提供室内备选？  
- 博物馆之间换乘是否超过用户「少走路」约束？  
- 是否出现未在调研笔记里出现的「幻觉景点」？

假设第一次：**未通过**——

```json
{
  "passed": false,
  "comments": [
    "下午有雨风险，但主行程仍偏户外步行，请强化室内备选并缩短换乘"
  ]
}
```

编排器看到 `passed=false` → **再次调用规划师**，并把 `comments` 一并传入。  
规划师改稿 → 管家再审 → `passed=true` → 写入 `final_plan` → 回复用户。

### 5.6 小白可以记住的「交互时间线」

| 时刻 | 谁在动 | 交互形式 | 白板变化 |
| --- | --- | --- | --- |
| T1 | 调研员 | 工具调用 + 写消息到白板 | `research_notes` 有了 |
| T2 | 规划师 | 读白板，生成初稿 | `itinerary_draft` 有了 |
| T3 | 管家 | 读初稿，写评审意见 | `review.passed=false` |
| T4 | 规划师 | 根据意见改稿 | `itinerary_draft` 更新 |
| T5 | 管家 | 再审 | `review.passed=true` |
| T6 | 编排器 | 汇总输出 | `final_plan` 有了 |

**你看：Agent 并没有玄学地「心灵感应」，全是「读白板 / 写白板 / 调工具 / 按图流转」。**

### 5.7 若改成「主管模式」会怎样？

同一需求，也可以只有一个主管：

1. 主管决定：先调研  
2. 调研回来后，主管决定：天气偏差，追加搜室内景点  
3. 再决定：让规划师写稿  
4. 再决定：要不要质检  

更灵活，但你要教主管「别无限派活」。流水线则更像固定 SOP，适合标准化产品。

---

## 6. 用伪代码感受「编排器」在干什么

下面不是某个框架的真实 API，而是**逻辑骨架**，方便你对照开源项目：

```python
# 伪代码：流水线 + 最多打回 2 次
state = {
    "user_request": "...",
    "research_notes": None,
    "itinerary_draft": None,
    "review": {"passed": False, "comments": []},
    "final_plan": None,
}

state["research_notes"] = research_agent.run(state["user_request"])

for attempt in range(2):
    state["itinerary_draft"] = planner_agent.run(
        notes=state["research_notes"],
        comments=state["review"]["comments"],
    )
    state["review"] = critic_agent.run(state["itinerary_draft"])
    if state["review"]["passed"]:
        break

state["final_plan"] = state["itinerary_draft"]
reply_to_user(state["final_plan"])
```

换成框架时：

- **CrewAI**：把三段写成三个 `Agent` + 三个 `Task`，用 Crew 顺序执行  
- **LangGraph**：三个节点 + 一条「质检失败则回到规划师」的边  
- **AutoGen / 群聊**：三个人在 GroupChat 里轮流说话，由 speaker selection 决定下一个谁说  

---

## 7. 什么时候该上多 Agent？什么时候不要？

| 更适合多 Agent | 更适合单 Agent |
| --- | --- |
| 步骤多、角色冲突（又写又审） | 任务短、工具少 |
| 需要互查提高正确率 | 延迟和成本极度敏感 |
| 不同权限/不同工具集 | 强一致的单一对话体验 |
| 要并行（搜 A 的同时搜 B） | 逻辑就是一条直线 |

**经验法则**：能用「一个 Agent + 好 Skills + 好工具」解决的，先别上多 Agent。多 Agent 的成本是：更多提示词、更多调度、更难调试。可参考 [AI Harness]({{ '/2026/07/30/ai-harness/' | relative_url }}) 里对评测与脚手架的讨论——协作复杂之后，可观测性更重要。

---

## 8. GitHub 开源参考索引（按「你想学什么」选）

> 仓库会演进，以下以「学协作模式」为目的推荐；点进 README 看最新用法即可。星标数量会变，文中不写死具体数字。

### 8.1 先看协作模式（强烈建议）

| 项目 | 地址 | 你能学到什么 |
| --- | --- | --- |
| **CrewAI** | [github.com/crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) | 角色团队（Crew）、任务交接，小白最友好的「小组隐喻」 |
| **LangGraph** | [github.com/langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) | 用图表达多 Agent：分支、循环、人工审批、状态持久化 |
| **OpenAI Agents SDK** | [github.com/openai/openai-agents-python](https://github.com/openai/openai-agents-python) | 轻量 **Handoff**、guardrail、会话；从早期 Swarm 思路演进而来 |
| **Microsoft AutoGen** | [github.com/microsoft/autogen](https://github.com/microsoft/autogen) | 多 Agent **对话/群聊**、可定制谁下一个发言 |
| **AG2**（社区延续线） | [github.com/ag2ai/ag2](https://github.com/ag2ai/ag2) | 与 AutoGen 生态相关的对话式多智能体实践（关注与官方 AutoGen 的定位差异） |

### 8.2 「仿真公司/软件团队」类（看角色怎么拆）

| 项目 | 地址 | 亮点 |
| --- | --- | --- |
| **MetaGPT** | [github.com/FoundationAgents/MetaGPT](https://github.com/FoundationAgents/MetaGPT)（亦见历史仓库 geekan/MetaGPT） | 按产品经理/架构师/工程师等角色协作做软件 |
| **ChatDev** | [github.com/OpenBMB/ChatDev](https://github.com/OpenBMB/ChatDev) | 虚拟软件公司，聊天式开发流程，适合理解「角色 + 阶段」 |

### 8.3 协议与互操作（进阶）

| 项目 / 协议 | 地址 | 亮点 |
| --- | --- | --- |
| **MCP** | [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) 等 | Agent **连工具**的开放协议（本站[专文]({{ '/2026/07/28/function-calling-mcp-skills/' | relative_url }})） |
| **A2A（Agent2Agent）** | 搜索 `a2aproject/A2A` 或 Google A2A 官方仓库 | 让**不同框架里的 Agent** 互相派任务（跨系统协作） |
| **Google ADK** | [github.com/google/adk-python](https://github.com/google/adk-python) | 层级 Agent、与 GCP 生态结合；常提到对 A2A/MCP 的支持 |

### 8.4 清单型导航（怕漏看）

| 列表 | 地址 |
| --- | --- |
| Awesome Agent Orchestration | [github.com/vivy-yi/awesome-agent-orchestration](https://github.com/vivy-yi/awesome-agent-orchestration) |
| LangChain 对框架的综述（官方视角） | [langchain.com/resources/ai-agent-frameworks](https://www.langchain.com/resources/ai-agent-frameworks) |

### 8.5 怎么选？（极简决策）

| 你的目标 | 更建议先看 |
| --- | --- |
| 最快搭一个「小组干活」Demo | **CrewAI** |
| 要生产级状态、回环、人工确认 | **LangGraph** |
| 要客服式技能切换 | **OpenAI Agents SDK（Handoff）** |
| 想研究「Agent 开会讨论」 | **AutoGen / AG2** |
| 想看「虚拟公司做软件」故事线 | **MetaGPT / ChatDev** |

---

## 9. 和本站其它文章怎么串起来

| 文章 | 关系 |
| --- | --- |
| [AI Agent]({{ '/2026/04/15/ai-agent/' | relative_url }}) | 单个智能体从哪来、长什么样 |
| [Function Calling / MCP / Skills]({{ '/2026/07/28/function-calling-mcp-skills/' | relative_url }}) | 单个 Agent 如何用工具与手册 |
| [AI Harness]({{ '/2026/07/30/ai-harness/' | relative_url }}) | 复杂 Agent 系统如何评测与兜底 |
| [WorkBuddy]({{ '/2026/07/30/workbuddy/' | relative_url }}) | 产品形态里的桌面 Agent（可对照「单助手」体验） |
| **本文** | **多个 Agent 如何组织与交互** |

---

## 10. 一页纸复习

1. 多 Agent = **分工明确的小团队**，不是多个嘴巴闲聊。  
2. 交互载体主要是：**消息、共享状态、Handoff、工具结果**。  
3. 五种组织：**流水线、主管分派、辩论互评、交接、层级外包**。  
4. 必须约定：**角色、I/O 格式、停止条件、权限、单一真相源**。  
5. 例子里三个角色通过 **写白板 + 质检打回** 完成出游计划——这就是协作的全部秘密。  
6. 上手开源：先 **CrewAI** 建立直觉，再 **LangGraph** 学可控编排。

---

## 11. 结语

多智能体听起来像科幻，落地时非常「工程」：  
**拆角色 → 定交接格式 → 选一种拓扑 → 给停止条件 → 留下日志好排障。**

如果你读完「上海一日游」那张时间表，觉得「这不就是工单系统吗」——恭喜，你已经懂了。  
下一步可以打开 [CrewAI](https://github.com/crewAIInc/crewAI) 或 [LangGraph](https://github.com/langchain-ai/langgraph) 的 Quickstart，把本文的三个角色真正跑起来。
