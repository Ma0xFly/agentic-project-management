---
name: apm-communication
description: Agent 通信标准和基于文件的 Message Bus 协议，用于结构化多 Agent 协作。
---

# APM {VERSION} - Communication Skill

## 1. 目标

本 skill 定义 APM 中 Agent 的通信标准和基于文件的 Message Bus 协议。Planner、Manager 和 Worker 都必须读取并遵守本文件。

非 APM agent 也可以参与 Message Bus 通信，方法见同目录下的 `bus-integration.md`。

---

## 2. Agent 与用户通信

### 2.1 直接沟通

和用户沟通时，使用自然中文。说明发生了什么、做出了什么判断、下一步要做什么。

当需要用户操作时，必须明确说明：

- 在哪个 Agent 会话中操作；
- 运行什么命令；
- 是否需要附加 agent-id；
- 预期结果是什么。

命令使用代码块，路径、字段和值使用行内代码。

### 2.2 可见推理

在关键决策点，先向用户展示你的分析，再执行动作。用户需要能审查你的判断、权衡和风险。

可见推理不等于暴露内部隐藏推理，而是用可审查的方式说明：

- 你正在评估什么；
- 关键证据是什么；
- 有哪些取舍；
- 你得出的结论是什么。

### 2.3 术语边界

APM 正式术语可以直接使用，例如 Task、Stage、Worker、Manager、Planner、Handoff、Message Bus。

不要在用户输出中暴露内部章节编号、流程步骤名或决策分支编号。应当用自然语言说明当前动作，而不是说“我正在执行第 3.2 节”。

---

## 3. Agent 与系统通信

写入 APM artifacts 时，遵守对应指南定义的结构，包括：

- `Spec`
- `Plan`
- `Tracker`
- Task Log
- Task Report
- Handoff Log
- Bus 文件

结构化字段、YAML frontmatter、路径和状态值保持英文原值，便于工具链和后续流程识别。自由文本内容使用中文。

---

## 4. Message Bus 协议

Message Bus 位于：

```text
.apm/bus/
```

Bus 文件要么为空，表示没有待处理消息；要么包含一条等待交付的消息。

写入 outgoing bus 前，先清空 incoming bus。写入前总是先读取目标 bus 文件，避免跨平台文件工具无法识别空文件。

### 4.1 Bus 身份

Agent 身份来自目录名：

```text
.apm/bus/<agent-slug>/
```

Worker 必须确认任务中的 `agent` 字段与当前目录匹配。如果不匹配，拒绝执行并提示用户切换到正确 Worker。

### 4.2 Agent ID 解析

`apm-4-check-tasks` 和 `apm-5-check-reports` 可接受 `[agent-id]` 参数。解析顺序：

1. 精确匹配 `.apm/bus/` 目录名；
2. 前缀匹配；
3. 最合理的模糊匹配。

如果只有一个合理候选，直接使用。如果有多个候选，列出并询问用户。如果没有 bus 目录，说明 Message Bus 尚未初始化。

### 4.3 Agent Slug 格式

Agent slug 从 `Plan` 的 Workers 字段派生：

- 转小写；
- 空格替换为连字符；
- 保持 kebab-case。

示例：

```text
Frontend Agent -> frontend-agent
Backend Agent -> backend-agent
```

Manager 固定使用：

```text
manager
```

---

**Skill 结束**
---

## 中文本土化与原版契约保留说明

本文件采用“双层结构”：上半部分是面向中文使用者的本土化执行说明，下半部分保留上游 APM 原版完整行为契约。

执行时必须遵守以下规则：

- 中文部分用于快速理解角色、流程和关键动作，不能替代后文的原版完整契约。
- 如果中文说明没有覆盖某个细节，必须继续遵守后文原版契约中的对应规则。
- 如果中文说明与原版契约存在理解差异，采用更严格、更具体、更能约束 Agent 行为的规则。
- 不得因为已经阅读中文说明而跳过后文的审批门槛、上下文边界、依赖判定、日志格式、交接流程或验证标准。

<!-- APM_CN_ORIGINAL_CONTRACT_START -->

# Original APM Behavioral Contract (Preserved)

以下为上游 APM 原版内容，保留用于维持原项目的完整流程约束、Agent 行为边界和可回溯性。
---
name: apm-communication
description: Agent communication standards and file-based Message Bus protocol for structured inter-agent messaging.
---

# APM {VERSION} - Communication Skill

## 1. Overview

**Reading Agent:** Planner, Manager, Worker

This skill defines agent communication standards and the file-based Message Bus protocol. It covers communication models, bus identity, and shared message formats. Agent-specific delivery and reporting procedures are defined in each agent's guides.

Agents not managed by APM can participate in bus communication by creating their own agent directory under `.apm/bus/`. See `bus-integration.md` alongside this skill for the integration guide.

---

## 2. Agent-to-User Communication

### 2.1 Direct Communication

When communicating with the User - asking questions, requesting actions, providing status updates, presenting completions - use natural language adapted to the situation. Explain what happened, what was decided, and what happens next. There are no rigid templates; adapt phrasing to what the situation requires while conveying necessary information.

When directing Users to perform actions (run commands, switch chats, review artifacts), provide specific actionable guidance naturally: which command, in which agent's chat, with what arguments. Present commands the User needs to run in code blocks so they are easy to copy. Use inline code for file paths, values, and references within prose. When multiple actions are needed (open a new chat, run initiation, check tasks), list them clearly with enough spacing to distinguish each step. When the action requires a new chat, include the platform guidance per {NEW_CHAT_GUIDANCE}.

Communication at workflow transitions should orient the User: what was just completed, what comes next, and what action is needed. Adapt naturally to the moment rather than following a fixed format.

### 2.2 Visible Reasoning

At procedural decision points, present your analysis visibly in chat before acting. The User needs to understand why you are making each decision - explain your assessments, justify your choices, and surface trade-offs so they can review and audit your reasoning and redirect if needed. Reasoning quality correlates with output quality. Internal reasoning or thinking may reach conclusions before visible chat output begins - but visible analysis in chat must still walk through the reasoning that led to those conclusions. Present how you arrived at each decision, not just what you decided. The User cannot audit or redirect decisions that appear in chat as given.

When a procedure prescribes specific headers for reasoning, present those headers visibly and address each section beneath them. When a procedure describes aspects to cover without prescribing headers, cover all indicated aspects using whatever format suits the content - prose, lists, tables, or any combination. In both cases, the output is analysis presented for the User's review. When no reasoning frame is provided, present what you are assessing, the key considerations, and your conclusion.

### 2.3 Terminology Boundaries

Formal APM terms - consistently capitalized words in APM commands and guides like Task, Stage, Worker, Manager - are part of the agent's public vocabulary. Use them naturally when communicating. All other language is natural prose; standard English capitalization applies but confers no formal status.

The following are internal authoring structure - use them for navigation but never surface them in User-facing output:
- Section references (§N.M).
- Procedure names and named sections from your guides.
- Step labels and checkpoint names.
- Decision categories.

When transitioning between sections, describe what you are doing and why rather than announcing which section you are executing. Describe your findings and move naturally into the next topic rather than stating "Beginning [section name]" or "Entering [step name]."

Reasoning frame headers prescribed by your procedures are always surfaced as defined per §2.2 Visible Reasoning. These are analytical output structure, not section announcements.

---

## 3. Agent-to-System Communication

When writing to APM artifacts (Spec, Plan, Tracker, Task Logs, bus files), follow the structural format defined by the relevant guide's structural specifications section or the bus protocol in §4 Message Bus Protocol. Artifact content is technical, formal, structured, and precise. Internal procedure vocabulary does not appear in artifacts - use natural descriptive language for any free-text fields.

---

## 4. Message Bus Protocol

Bus directories and files are initialized during the Planning Phase. Bus files are either empty (no message present) or contain a message awaiting delivery. Before writing to an outgoing bus file, an agent clears its incoming bus file. Always read a bus file before writing to it - this ensures the platform's file tools recognize the file and avoids write failures on empty or cleared files.

### 4.1 Bus Identity Standards

Agent identity is derived from the agent directory name (`.apm/bus/<agent-slug>/`). Workers validate by confirming the directory matches their registered `agent`. If the agent directory does not match, reject the message and inform the User of the mismatch.

### 4.2 Agent ID Resolution

When `/apm-4-check-tasks` or `/apm-5-check-reports` accept an `[agent-id]` argument, resolve it against `.apm/bus/` directory names: exact match, then prefix, then best plausible match. When only one plausible candidate exists, resolve to it. When multiple candidates are plausible, list them and ask the User. When no bus directories exist, inform that the Message Bus is not initialized.

### 4.3 Agent Slug Format

Agent slugs are derived from the Worker names listed in the Plan Workers field by converting to lowercase and replacing spaces with hyphens. Examples: `Frontend Agent` → `frontend-agent`, `Backend Agent` → `backend-agent`. The Manager's own directory uses the slug `manager`.

---

**End of Skill**
