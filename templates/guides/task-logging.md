# APM {VERSION} - Task Logging Guide

## 1. 适用角色

**Reading Agent:** Worker

本指南定义 Worker 如何写 Task Log 和 Task Report。Task Log 保存任务级事实，Task Report 是给 Manager 的简短交付消息。

---

## 0. 中文日志和报告强制规则

Worker 写入的 Task Log 和 Task Report 必须使用中文正文。

- `## Summary`、`## Details`、`## Output`、`## Validation`、`## Issues`、`## Compatibility Concerns`、`## Important Findings` 等结构标题可以保留英文，以保持 APM 格式兼容。
- 标题下面的结果摘要、执行细节、文件变更说明、验证结果、问题、兼容性风险和重要发现必须使用中文。
- 如果验证命令输出、错误消息或代码片段是英文，可以原样引用；但必须在旁边用中文说明它代表什么、是否通过、还有什么风险。
- Task Report 的正文也必须中文，不能只写英文状态句。

---

## 2. 状态和标记

### 2.1 Outcome

`status` 只能使用：

- `Success`：目标达成，所有验证通过；
- `Partial`：有进展但未完成，需要指导；
- `Failed`：目标未达成，已尝试但无法解决。

不要因为“做了很多努力”就标记 `Success`。状态只看结果。

### 2.2 Flags

`important_findings`：

- 发现 Task Prompt 未覆盖的重要事实；
- 发现可能影响其他 Tasks 或 Workers 的风险；
- 发现依赖、约束、用户纠正或系统行为差异。

`compatibility_issues`：

- 输出与现有代码、接口、约定、依赖或运行环境冲突；
- 引入 breaking change 或迁移要求；
- 发现集成风险。

不确定时设为 `true`。误报比漏报更容易处理。

---

## 3. Task Log

位置由 Task Prompt 的 `log_path` 指定，通常为：

```text
.apm/memory/stage-<NN>/task-<NN>-<MM>.log.md
```

YAML frontmatter：

```yaml
---
stage: <N>
task: <M>
title: <Task title from Plan>
agent: <agent-slug>
status: Success | Partial | Failed
important_findings: true | false
compatibility_issues: true | false
---
```

正文模板：

```markdown
# Task <N>.<M> - <Title>

## Summary

用 1-2 句话说明主要结果。

## Details

说明完成的工作、关键决策、执行步骤和使用的 subagent（如有）。

## Output

- 新增或修改的文件路径
- 配置变化
- 交付物
- 必要代码片段（尽量不超过 20 行）

## Validation

列出执行的测试、构建、检查命令和结果。

## Issues

列出阻塞、错误或未解决问题；没有则写 `None`。

## Compatibility Concerns

仅当 `compatibility_issues: true` 时包含。

## Important Findings

仅当 `important_findings: true` 时包含。
```

---

## 4. Task Report

写入：

```text
.apm/bus/<agent-slug>/report.md
```

写入前先清空 incoming Task Bus：

```text
.apm/bus/<agent-slug>/task.md
```

YAML frontmatter：

```yaml
---
stage: <N>
task: <M>
agent: <agent-slug>
status: Success | Partial | Failed
log_path: ".apm/memory/stage-<NN>/task-<NN>-<MM>.log.md"
important_findings: true | false
compatibility_issues: true | false
---
```

正文用 1-2 句话总结结果，并引用 Task Log 路径。详细内容放 Task Log，不要塞进 Report。

完成后提示用户回到 Manager 会话运行：

```text
/apm-5-check-reports <agent-id>
```

Codex CLI 使用：

```text
$apm-5-check-reports <agent-id>
```

---

## 5. Batch Report

Batch report 写入同一个 Report Bus。

YAML frontmatter：

```yaml
---
batch: true
batch_size: <N>
completed: <M>
stopped_early: true | false
tasks:
  - stage: 1
    task: 1
    status: Success
  - stage: 1
    task: 2
    status: Failed
  - stage: 1
    task: 3
    status: "Not started"
---
```

正文包含：

```markdown
# Batch Report

## Summary

## Task Outcomes

### <Title>

**Status:** Success | Partial | Failed
**Task Log:** `<log_path>`
```

---

**Guide 结束**
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
# APM {VERSION} - Task Logging Guide

## 1. Overview

**Reading Agent:** Worker

This guide defines how you log Task outcomes and report results. Task Logs capture Task-level context using structured markdown files, enabling the Manager to track progress and make review decisions without parsing raw code or chat history.

### 1.1 Outputs

- *Task Log:* Structured log at `.apm/memory/stage-<NN>/task-<NN>-<MM>.log.md` capturing outcome, validation, deliverables, and flags.
- *Task Report:* Concise summary written to the Report Bus for the Manager to process.

---

## 2. Operational Standards

### 2.1 Flag Assessment Standards

Boolean flags in YAML frontmatter signal conditions requiring Manager attention. Set flags based on what you observed during execution relative to your Task Prompt and working context.

**`important_findings`:** Set to `true` when execution revealed information not in your Task Prompt that seems project-relevant, you discovered dependencies, risks, or constraints your Task Prompt didn't account for, or something suggests other Tasks or agents might be affected.

**`compatibility_issues`:** Set to `true` when your output conflicts with existing code, patterns, or conventions you touched, you discovered integration concerns that might affect other parts of the system, or breaking changes or migration requirements resulted from your work.

**Default:** When uncertain whether a finding warrants a flag, set to `true`. False negatives hurt coordination more than false positives.

### 2.2 Outcome Standards

Status reflects whether the objective was achieved. Select based on end state, not effort expended.

- *Success:* Objective achieved, all validation passed.
- *Partial:* Some progress made but incomplete; you need guidance to continue.
- *Failed:* Objective not achieved; you attempted but could not resolve the issue.

Use Partial when: validation is ambiguous, important findings emerged that could affect other Tasks, iteration stalled with recurring failures, or approach uncertainty depends on factors outside your scope. Continue iterating (do not log yet) when validation failed but the cause is clear and fixable, no findings require Manager awareness, and progress is being made.

### 2.3 Detail Level Standards

Task Logs serve the Manager's coordination needs, not archival documentation. Ask: does this detail help the Manager understand what was accomplished? Would it affect the Manager's next review decision? Can it be found by reading the referenced artifacts directly?

**Default:** Prefer concise but comprehensive summaries with artifact references over verbose inline content. Reference artifacts by path rather than including large code blocks. Include code snippets only for novel, complex, or critical logic (20 lines or fewer). For error messages, include relevant stack traces or diagnostic details.

---

## 3. Task Logging Procedure

Two sequential steps after Task completion: write the Task Log, then deliver the Task Report via the bus. Execute after Task completion per `{GUIDE_PATH:task-execution}` §3.6 Task Completion.

### 3.1 Task Log Procedure

After Task execution, populate the Task Log at the path provided in the Task Prompt (`log_path`).

Perform the following actions:
1. From the completion assessment already presented in chat per `{GUIDE_PATH:task-execution}` §3.6 Task Completion, determine what to capture in the Task Log.
2. Complete YAML frontmatter fields:
   - Set `status` per §2.2 Outcome Standards.
   - Set `important_findings` and `compatibility_issues` per §2.1 Flag Assessment Standards.
   - Set `stage`, `task`, `title`, and `agent` from the Task Prompt.
3. Complete markdown body sections per §4.1 Task Log Format. Always include: Summary, Details, Output, Validation, Issues. Include conditional sections (Compatibility Concerns, Important Findings) only when their corresponding flag is `true`.
4. Write the Task Log to `log_path`.

### 3.2 Task Report Delivery

Perform the following actions:
1. Clear the incoming Task Bus: truncate `.apm/bus/<agent-slug>/task.md` via terminal (e.g., `truncate -s 0` or shell redirection).
2. Read the Report Bus, then write the Task Report to it: `.apm/bus/<agent-slug>/report.md`. The report is a concise summary - key outcome, status, log path, and any flags. Detail belongs in the Task Log.
3. Direct the User to deliver the report to the Manager per `{SKILL_PATH:apm-communication}` §2.1 Direct Communication - provide both `/apm-5-check-reports <agent-id>` for targeted retrieval and `/apm-5-check-reports` as the general command, since multiple Workers may finish concurrently.

For batch execution, write a single batch report per §4.3 Batch Report Format after completing all Tasks (or stopping on failure).

---

## 4. Structural Specifications

### 4.1 Task Log Format

**Location:** `.apm/memory/stage-<NN>/task-<NN>-<MM>.log.md`

**Naming Convention:**
- `<NN>`: Stage number, zero-padded (e.g., 01, 02).
- `<MM>`: Task number within Stage, zero-padded (e.g., 01, 02).

**YAML Frontmatter Schema:**

```yaml
---
stage: <N>
task: <M>
title: <Task title from Plan>
agent: <agent-slug>
status: Success | Partial | Failed
important_findings: true | false
compatibility_issues: true | false
---
```

**Field Descriptions:**
- `stage`: Stage number from the Task Prompt.
- `task`: Task number from the Task Prompt.
- `title`: Task title from the Task Prompt.
- `agent`: Your agent identifier.
- `status`: Task outcome per §2.2 Outcome Standards. `Success`, `Partial`, or `Failed`.
- `important_findings`: Whether discoveries have implications beyond current Task scope per §2.1 Flag Assessment Standards.
- `compatibility_issues`: Whether output conflicts with existing systems per §2.1 Flag Assessment Standards.

**Markdown Body Template:**

```markdown
# Task <N>.<M> - <Title>

## Summary
[1-2 sentences describing main outcome]

## Details
[Work performed, decisions made, steps taken in logical order. Note subagent usage when applicable.]

## Output
- File paths for created/modified files
- Code snippets (if necessary, ≤20 lines)
- Configuration changes
- Results or deliverables

## Validation
[Description of validation performed and result]

## Issues
[Specific blockers or errors encountered, or "None"]

## Compatibility Concerns
[Only include if compatibility_issues: true]
[Description of compatibility issues identified]

## Important Findings
[Only include if important_findings: true]
[Project-relevant discoveries that Manager must know]
```

### 4.2 Task Report Format

Task Reports are concise summaries written to the Report Bus for the Manager to process. Detail belongs in the Task Log - the report provides enough for the Manager to assess the outcome and locate the log.

**Location:** `.apm/bus/<agent-slug>/report.md`

**YAML Frontmatter Schema:**

```yaml
---
stage: <N>
task: <M>
agent: <agent-slug>
status: Success | Partial | Failed
log_path: ".apm/memory/stage-<NN>/task-<NN>-<MM>.log.md"
important_findings: true | false
compatibility_issues: true | false
---
```

**Field Descriptions:**
- `stage`: Stage number from the Task Prompt.
- `task`: Task number from the Task Prompt.
- `agent`: Your agent identifier.
- `status`: Task outcome per §2.2 Outcome Standards.
- `log_path`: Path to the Task Log for this Task.
- `important_findings`: Same value as the Task Log.
- `compatibility_issues`: Same value as the Task Log.

**Markdown Body:** 1-2 sentences summarizing the outcome. Reference the Task Log for detail.

For batch reports, use §4.3 Batch Report Format instead.

### 4.3 Batch Report Format

When completing a batch of Tasks (or stopping early on failure), the Report Bus file uses this structure.

**Location:** `.apm/bus/<agent-slug>/report.md`

**YAML Frontmatter Schema:**

```yaml
---
batch: true
batch_size: <N>
completed: <M>
stopped_early: true | false
tasks:
  - stage: 1
    task: 1
    status: Success
  - stage: 1
    task: 2
    status: Failed
  - stage: 1
    task: 3
    status: "Not started"
---
```

**Field Descriptions:**
- `batch`: Always `true` for batch reports.
- `batch_size`: Total Tasks in the batch.
- `completed`: Tasks that were executed (excludes unstarted).
- `stopped_early`: Whether the batch stopped before completing all Tasks.
- `tasks[].stage`: Stage number.
- `tasks[].task`: Task number within Stage.
- `tasks[].status`: `Success`, `Partial`, `Failed`, or `"Not started"` for unexecuted Tasks.

**Markdown Body Template:**

```markdown
# Batch Report

## Summary
[Brief overview: X of Y Tasks completed, stopped early if applicable]

## Task Outcomes

### <Title>
**Status:** [Success | Partial | Failed]
**Task Log:** `<log_path>`
[1-2 sentence summary of outcome]

...

## Batch Notes
[Any cross-cutting observations, patterns, or issues affecting multiple Tasks]
```

If the batch stopped early due to a Failed Task, indicate which Task caused the stop and list remaining Tasks as "Not started (batch stopped)."

---

## 5. Content Guidelines

### 5.1 Good vs Poor Logging
- *Summary:* "Made some changes and fixed issues" → "Implemented POST /api/users with validation. All tests passing."
- *Details:* "I worked on the endpoint and there were some issues" → "Added registration route with email/password validation using express-validator"
- *Output:* "Changed some files" → "Modified: `routes/users.js`, `server.js`"
- *Validation:* "It works now" → "Test suite: 5/5 passing. Manual testing confirmed expected responses."

### 5.2 Common Mistakes

- *Forgetting conditional sections:* When a flag is `true`, include the corresponding section (Compatibility Concerns, Important Findings).
- *Missing artifact references:* When deliverables are produced, list file paths in the Output section.
- *Deferred batch logging:* In batch execution, write each Task Log immediately after completing that Task - before starting the next. Deferring all logging to the end of a batch risks context loss if auto-compaction occurs mid-batch.

---

**End of Guide**
