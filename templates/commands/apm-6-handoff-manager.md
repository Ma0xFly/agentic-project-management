---
command_name: handoff-manager
description: 执行 APM Manager Handoff。
---

# APM {VERSION} - Manager Handoff 命令

## 1. 目标

当 Manager 的上下文窗口接近上限、发生自动压缩、输出质量下降，或用户要求交接时，执行本命令。

你需要创建两类产物：

- **Handoff Log**：记录当前 Manager 实例已经完成、决定和观察到的内容，保存到 `.apm/memory/handoffs/manager/`。
- **Handoff Prompt**：写入 `.apm/bus/manager/handoff.md`，指导下一个 Manager 如何重建上下文。

新的 Manager 不是只读 Handoff Log，而是结合规划文档、指南、skill、Task Logs 和 Handoff Log 重建上下文。

---

## 0. 中文交接产物强制规则

Manager Handoff 产物必须使用中文正文。

- Handoff Log 中的已完成事项、历史判断、用户偏好、协调经验、风险和版本控制状态说明必须中文。
- Handoff Prompt 中的当前状态、下一步动作、待读文件、未决报告和 active Workers 说明必须中文。
- YAML 字段、路径、命令、Task ID、Agent 名称、分支名、状态值可以保留英文。
- 如果引用英文命令输出、错误消息或代码片段，必须补充中文解释。

---

## 2. 执行流程

### 2.1 创建 Handoff Log

执行以下动作：

1. 判断当前 Manager instance number 和下一个 Manager instance number。
2. 创建 Handoff Log，记录过去发生的事情。内容使用过去时，不写当前待办状态。
3. 必须覆盖：
   - 已协调的 Stages；
   - 已审查的 Tasks；
   - 已完成的派发循环；
   - 已发生的 Worker Handoffs；
   - 当前实例发生过的自动压缩或恢复事件；
   - 从 Tracker 提取的版本控制状态；
   - 用户偏好和沟通模式；
   - 重要协调判断、尝试过的方法和经验。

### 2.2 创建 Handoff Prompt

执行以下动作：

1. 创建 Handoff Prompt，记录当前状态和下一步动作。内容必须可执行、使用现在时。
2. 必须覆盖：
   - 当前 Stage 和进度；
   - outstanding Tasks 的完整信息；
   - 正在审查的报告和未决结论；
   - active Workers 和派发状态；
   - 新 Manager 必须读取的 Task Logs 和文件；
   - 立即下一步应该做什么。

### 2.3 用户审查和完成

1. 写入 Handoff Prompt：

   ```text
   .apm/bus/manager/handoff.md
   ```

2. 向用户展示 Handoff Log 路径和 Handoff Bus 路径，请用户审查。
3. 指导用户新开 Manager 会话并运行：

   ```text
   /apm-2-initiate-manager
   ```

   Codex CLI 使用：

   ```text
   $apm-2-initiate-manager
   ```

4. 如果用户要求修改，更新产物后再结束。完成后，当前 Manager 不再继续协调。

---

## 3. Handoff Log 结构

位置：

```text
.apm/memory/handoffs/manager/handoff-<NN>.log.md
```

YAML frontmatter：

```yaml
---
agent: manager
outgoing: <N>
incoming: <N+1>
handoff: <N>
stage: <N>
---
```

正文结构：

```markdown
# Manager Handoff <N> (Manager <N> -> Manager <N+1>)

## Summary

## Working Context

## Worker Handoffs

## Version Control State

## Working Notes
```

---

## 4. Handoff Prompt 结构

写入：

```text
.apm/bus/manager/handoff.md
```

必须包含：

- outgoing 和 incoming instance number；
- 需要读取的 Handoff Log；
- 需要读取的当前 Stage Task Logs；
- previous-stage dependency context 的处理方式；
- 当前 Stage、进度、阻塞点、working notes；
- 立即下一步；
- 要求新 Manager 先用中文输出简洁理解摘要，然后继续协调。

---

**命令结束**
---

## 中文执行优先与原版契约保留说明

本文件采用“双层结构”：上半部分是面向中文使用者的本土化执行层，下半部分保留上游 APM 原版完整行为契约。

执行时必须遵守以下优先级：

- **中文执行层是本分叉的实际执行口径和输出语言权威。** 不得因为后文保留英文原版契约，就把面向用户或项目产物的正文改成英文。
- **所有面向用户的解释、提问、分析、总结、风险说明、审查意见和下一步指令必须使用中文。**
- **所有 APM 项目产物正文必须使用中文。** 包括但不限于 `.apm/spec.md`、`.apm/plan.md`、`{RULES_FILE}` 中的 APM 规则、Task Prompt、Task Log、Task Report、Handoff Log、Recovery Summary、Stage Summary、Memory Notes 和 Working Notes。
- 文件路径、命令、代码标识、YAML 字段、Markdown 结构标题、状态值、Agent 名称、Task ID、mermaid 语法和协议字段可以保留英文，以保证 APM 格式兼容；但这些字段对应的说明性正文必须中文。
- 英文原版契约只用于补足流程细节和约束强度，例如审批门槛、上下文边界、依赖判定、日志格式、交接流程和验证标准；**它不具有输出语言优先级**。
- 如果中文执行层没有覆盖某个流程细节，参考后文英文原版契约补齐；补齐时仍必须按中文执行层的语言规则输出中文产物。
- 如果中文执行层与英文原版契约存在理解差异，采用更严格、更具体、更能约束 Agent 行为的规则，但不得违反“中文输出和中文项目产物”要求。

<!-- APM_CN_ORIGINAL_CONTRACT_START -->

# Original APM Behavioral Contract (Preserved as Process Fallback)

以下为上游 APM 原版内容，仅作为流程完整性和约束强度的兜底参考。执行和产物输出必须遵守上方中文执行层的语言优先级。
---
command_name: handoff-manager
description: Perform a Handoff with an APM Manager.
---

# APM {VERSION} - Manager Handoff Command

## 1. Overview

This command initiates the Handoff procedure for a Manager approaching context window limits. You create two artifacts:
- **Handoff Log:** Working context not captured in planning documents or Task Logs, stored in `.apm/memory/handoffs/manager/`.
- **Handoff prompt:** Written to the Handoff Bus, instructing the incoming Manager to reconstruct context procedurally.

The incoming Manager rebuilds working context from planning documents, guides, skills, Task Logs, and the Handoff Log - not from the Handoff Log alone.

---

## 2. Handoff Procedure

Execute when User initiates Handoff.

### 2.1 Handoff Log Creation

Perform the following actions:
1. Determine instance numbers: your current instance number and incoming Manager instance number (yours + 1).
2. Create Handoff Log per §3 Handoff Log Structure, capturing **past actions** - what was done, decided, and observed. Content is strictly past tense; current state belongs in the handoff prompt.
   - Coordination overview: Stages managed, Tasks reviewed, dispatch cycles completed.
   - Tracked Worker Handoffs (which Workers, from which Stage) - most critical for dependency context treatment.
   - If auto-compaction occurred during this instance, note it and describe which portions of working context are reconstructed rather than first-hand from the summary.
   - VC state extracted from the Tracker in context: active branches, worktrees, pending merges.
   - User preferences and communication patterns.
   - Coordination insights, decisions made, approaches tried.

### 2.2 Handoff Prompt Creation

Perform the following actions:
1. Create handoff prompt per §4 Handoff Prompt Structure, capturing **current state** - what is happening now. Content is actionable and present-tense; past actions belong in the Handoff Log.
   - Outstanding Tasks in full: objectives, expected outputs, detailed instructions, review criteria, relevant Spec sections, dependency context, workspace information.
   - Mid-review progress and pending review outcomes.
   - Active Workers and their dispatch state.
   - Pointers to Task Logs and files for the incoming Manager to read.

### 2.3 User Review and Finalization

Perform the following actions:
1. Write handoff prompt to the Handoff Bus: `.apm/bus/manager/handoff.md`.
2. Present both artifacts to User: Handoff Log (file path) and handoff prompt (bus path). Request review and direct User to start a new chat and run `/apm-2-initiate-manager` - the incoming Manager will auto-detect the handoff prompt.
3. If modifications requested, update accordingly. This completes the outgoing Manager's duties.

---

## 3. Handoff Log Structure

Contains working context not captured in planning documents or Task Logs. The incoming Manager reconstructs primary context from artifacts - this file provides supplementary context.

**Location:** `.apm/memory/handoffs/manager/handoff-<NN>.log.md`

**YAML Frontmatter Schema:**
```yaml
---
agent: manager
outgoing: <N>
incoming: <N+1>
handoff: <N>
stage: <N>
---
```

**Field Descriptions:**
- `agent`: Always `manager`.
- `outgoing`: Current instance number.
- `incoming`: Next instance number.
- `handoff`: Handoff sequence number (equals the outgoing instance number).
- `stage`: Current Stage number.

**Body:**
- *Title:* `# Manager Handoff <N> (Manager <N> → Manager <N+1>)`. Each section uses `##` heading.
- *Summary:* Stages coordinated, Tasks reviewed, dispatch cycles completed.
- *Working Context.* Tracked Worker Handoffs table (Agent, Handoff Stage, current-Stage logs loaded, notes) with dependency context implication. VC state: active branches, worktrees, pending merges, base branch. Dispatch patterns.
- *Working Notes:* Coordination insights, User preferences, decisions made, approaches tried.

---

## 4. Handoff Prompt Structure

Written to `.apm/bus/manager/handoff.md`. The incoming Manager processes this prompt during auto-detection in the init command.

**Required content:**
- *Identity:* Outgoing and incoming instance numbers.
- *Rebuilding context:*
  1. Read Handoff Log - note tracked Worker Handoffs and VC state.
  2. Read current-Stage Task Logs (all agents).
  3. For previous-Stage dependency context encountered later: read the specific Task Log on demand. If the Task Log is insufficient, read referenced files to reconstruct context.
- *Current State:* Current Stage, Stage progress, next Task, blockers, working notes.
- *Immediate Next Action:* Specific coordination action to resume.
- *Closing instruction:* Output a concise understanding summary (project state, Worker Handoffs and implications, VC state, next action) then proceed with coordination.

---

**End of Command**
