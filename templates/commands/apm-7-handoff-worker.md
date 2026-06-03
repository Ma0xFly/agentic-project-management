---
command_name: handoff-worker
description: 执行 APM Worker Handoff。
---

# APM {VERSION} - Worker Handoff 命令

## 1. 目标

当 Worker 的上下文窗口接近上限、发生自动压缩、输出质量下降，或用户要求交接时，执行本命令。

你需要创建两类产物：

- **Handoff Log**：记录当前 Worker 实例的工作上下文，保存到 `.apm/memory/handoffs/<agent>/`。
- **Handoff Prompt**：写入 `.apm/bus/<agent-slug>/handoff.md`，指导下一个 Worker 如何恢复。

新的 Worker 需要结合 Handoff Log 和当前 Stage 的 Task Logs 恢复上下文。它还必须在第一份 Task Report 中说明自己是 Handoff 后的新实例，方便 Manager 正确处理依赖上下文。

---

## 0. 中文交接产物强制规则

Worker Handoff 产物必须使用中文正文。

- Handoff Log 中的已完成工作、技术判断、实现方式、已尝试方法、未记录细节和恢复事件必须中文。
- Handoff Prompt 中的当前状态、下一步动作、待读 Task Logs、mid-task 或 mid-batch 状态说明必须中文。
- YAML 字段、路径、命令、Task ID、Agent 名称、分支名、状态值可以保留英文。
- 如果引用英文命令输出、错误消息或代码片段，必须补充中文解释。

---

## 2. 执行流程

### 2.1 创建 Handoff Log

执行以下动作：

1. 判断当前 Worker instance number 和下一个 Worker instance number。
2. 创建 Handoff Log，记录过去发生的事情。内容使用过去时，不写当前待办状态。
3. 必须覆盖：
   - 当前实例完成的 Tasks；
   - 当前 Stage 中该 Worker 的进度；
   - 已形成的工作模式、技术判断和实现方式；
   - Task Logs 中没有记录但对后续执行有价值的技术细节；
   - 如果任务执行到一半，记录已完成步骤和尝试过的方法；
   - 当前实例是否发生过自动压缩或 Recover。

### 2.2 创建 Handoff Prompt

执行以下动作：

1. 创建 Handoff Prompt，记录当前状态和下一步动作。内容必须可执行、使用现在时。
2. 根据状态选择：
   - **Mid-Task**：说明任务仍在 `task.md` 中，列出已完成步骤和下一步。
   - **Mid-batch**：说明 batch 仍在 `task.md` 中，逐个列出哪些完成、哪些进行中、哪些未开始。
   - **Between-Tasks**：说明当前没有 active Task，等待用户运行 `apm-4-check-tasks`。
3. 包含 Handoff Log 路径、需要读取的当前 Stage Task Logs，以及第一份 Task Report 必须声明 Handoff 状态的提醒。

### 2.3 用户审查和完成

1. 写入 Handoff Prompt：

   ```text
   .apm/bus/<agent-slug>/handoff.md
   ```

2. 向用户展示 Handoff Log 路径和 Handoff Bus 路径，请用户审查。
3. 指导用户新开 Worker 会话并运行：

   ```text
   /apm-3-initiate-worker <agent-id>
   ```

   Codex CLI 使用：

   ```text
   $apm-3-initiate-worker <agent-id>
   ```

4. 如果用户要求修改，更新产物后再结束。完成后，当前 Worker 不再继续执行。

---

## 3. Handoff Log 结构

位置：

```text
.apm/memory/handoffs/<agent>/handoff-<NN>.log.md
```

YAML frontmatter：

```yaml
---
agent: <agent-slug>
outgoing: <N>
incoming: <N+1>
handoff: <N>
stage: <N>
---
```

正文结构：

```markdown
# <Display Name> Handoff <N> (<Display Name> <N> -> <Display Name> <N+1>)

## Summary

## Working Context

## Working Notes
```

---

## 4. Handoff Prompt 结构

写入：

```text
.apm/bus/<agent-slug>/handoff.md
```

必须包含：

- outgoing 和 incoming instance number；
- Handoff Log 路径；
- 当前 Stage 中需要读取的该 Worker Task Logs；
- 当前任务状态和继续方式；
- 第一份 Task Report 必须声明 Handoff 状态；
- 要求新 Worker 先用中文确认已读取上下文，再继续或等待任务。

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
command_name: handoff-worker
description: Perform a Handoff with an APM Worker.
---

# APM {VERSION} - Worker Handoff Command

## 1. Overview

This command initiates the Handoff procedure for a Worker approaching context window limits. You create two artifacts:
- **Handoff Log:** Working context from the current instance, stored in `.apm/memory/handoffs/<agent>/`.
- **Handoff prompt:** Written to the Handoff Bus, instructing the incoming Worker to reconstruct context.

The incoming Worker rebuilds working context from the Handoff Log and current Stage Task Logs - not from the Handoff Log alone.

The incoming Worker must indicate Handoff status in their first Task Report. This triggers the Manager's Handoff detection, which affects dependency context classification for future Task assignments.

---

## 2. Handoff Procedure

Execute when User initiates Handoff.

### 2.1 Handoff Log Creation

Perform the following actions:
1. Determine instance numbers: your current instance number and incoming Worker instance number (yours + 1).
2. Create Handoff Log per §3 Handoff Log Structure, capturing **past actions** - what was done, tried, and observed. Content is strictly past tense; current state belongs in the handoff prompt.
   - Tasks completed and Stage progress this instance.
   - Working context, patterns, and approaches established during this instance.
   - Technical notes not captured in Task Logs.
   - If mid-Task, include execution progress framed as past work (steps completed, approaches tried).
   - If auto-compaction occurred during this instance, note it and describe which portions of working context are reconstructed rather than first-hand from the summary.

### 2.2 Handoff Prompt Creation

Perform the following actions:
1. Create handoff prompt per §4 Handoff Prompt Structure, capturing **current state** - what is happening now. Content is actionable and present-tense; past actions belong in the Handoff Log.
2. Apply Worker Handoff asymmetry:
   - *Mid-Task:* "Read the Task from `task.md`, I completed steps 1-4, resume from step 5." Direct the incoming Worker to read the Task Bus file directly (intact since Task receipt). Include execution progress detail.
   - *Mid-batch:* The batch is still in `task.md`. Describe the state of each Task in the batch - which are complete (logs written), which is in progress and how far, and which have not been started. The incoming Worker reads the intact batch from the Task Bus and continues from where work left off.
   - *Between-Tasks:* "No active Task, await `/apm-4-check-tasks`." State context and readiness.
3. Include: Handoff Log path, instructions to read current Stage Task Logs, and reminder to indicate incoming Worker status in first Task Report (listing specific Task Log files loaded and, when previous Stages exist, noting that previous-Stage logs were not loaded).

### 2.3 User Review and Finalization

Perform the following actions:
1. Write handoff prompt to the Handoff Bus: `.apm/bus/<agent-slug>/handoff.md`.
2. Present both artifacts to User: Handoff Log (file path) and handoff prompt (bus path). Request review and direct User to start a new chat and run `/apm-3-initiate-worker <agent-id>` - the incoming Worker will auto-detect the handoff prompt.
3. If modifications requested, update accordingly. This completes the outgoing Worker's duties.

---

## 3. Handoff Log Structure

Contains working context from this instance that supports the incoming Worker's execution. Task Logs contain Task-specific details - this file provides instance-level context.

**Location:** `.apm/memory/handoffs/<agent>/handoff-<NN>.log.md`

**YAML Frontmatter Schema:**
```yaml
---
agent: <agent-slug>
outgoing: <N>
incoming: <N+1>
handoff: <N>
stage: <N>
---
```

**Field Descriptions:**
- `agent`: Worker identifier (kebab-case).
- `outgoing`: Current instance number.
- `incoming`: Next instance number.
- `handoff`: Handoff sequence number (equals the outgoing instance number).
- `stage`: Current Stage number.

**Body:**
- *Title:* `# <Display Name> Handoff <N> (<Display Name> <N> → <Display Name> <N+1>)`. Each section uses `##` heading. The display name is the Title Case form of the agent identifier (e.g., `frontend-agent` → `Frontend Agent`).
- *Summary:* Tasks completed count, current Stage, Stage progress for this Worker.
- *Working Context:* Patterns, approaches, or context established during this instance.
- *Working Notes:* Technical details, environment observations, or other context not captured in Task Logs.

---

## 4. Handoff Prompt Structure

Written to `.apm/bus/<agent-slug>/handoff.md`. The incoming Worker processes this prompt during auto-detection in the init command.

**Required content:**
- *Identity:* Outgoing and incoming instance numbers.
- *Rebuilding context:*
  1. Read Handoff Log - note working context and technical notes.
  2. Read current Stage Task Logs (this Worker's logs only).
  3. Do not load previous-Stage logs - the Manager provides comprehensive context via Task Prompts for cross-Stage dependencies.
- *Current State:* Current Stage, Tasks completed this instance, notes.
- *Continuation guidance:* Specific guidance for the incoming Worker about in-progress patterns or upcoming work.
- *Incoming Worker indication:* Remind incoming Worker to include Handoff status in first Task Report - state instance number, list specific Task Log files loaded, and when previous Stages exist note that previous-Stage logs were not loaded. This triggers Manager Handoff detection.
- *Immediate Next Action:* For mid-Task or mid-batch, instruct the incoming Worker to read the Task Bus and continue. For between-Tasks, state readiness to await `/apm-4-check-tasks`.
- *Closing instruction:* Confirm to User that Handoff Log and Stage context have been read, then state readiness.

---

**End of Command**
