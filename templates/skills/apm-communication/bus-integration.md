# 非 APM Agent 接入 Message Bus

非 APM agent 可以通过 `.apm/bus/` 参与任务交付和报告，但它们不参与 Memory、Handoff 和 Worker tracking。

## 接入步骤

1. 为 agent 创建目录：

   ```text
   .apm/bus/<agent-slug>/
   ```

2. 创建文件：

   ```text
   .apm/bus/<agent-slug>/task.md
   .apm/bus/<agent-slug>/report.md
   .apm/bus/<agent-slug>/handoff.md
   ```

3. Manager 可以把 Task Prompt 写入该 agent 的 `task.md`。

4. 该 agent 完成后，把 Task Report 写入 `report.md`。

## 限制

- 非 APM agent 不写 Task Log；
- 不执行 Handoff；
- 不出现在 Worker tracking 中；
- Manager 只把它当作可通信执行者处理。
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
# APM Bus System Integration Guide

## 1. Overview

**Reading Agent:** Any non-APM agent

This guide explains how non-APM agents can participate in an APM session through the Message Bus. If you are a standalone agent (not managed by APM's Manager) and need to communicate with APM-managed Workers or the Manager, follow this guide.

You participate at the communication level only - you receive work through the bus, execute it, and report back. You are not a Worker: you do not log to `.apm/memory/`, do not perform Handoff, and are not tracked in the Tracker. Your reports are your sole contribution to the session.

---

## 2. Setup

Create your own bus directory in `.apm/bus/`:
1. Choose a slug for your identity (lowercase, hyphenated name) that does not conflict with existing directories in `.apm/bus/`. Example: `external-reviewer`. This slug is your identifier for all bus communication - the Manager uses it to find your reports.
2. Create your bus directory: `.apm/bus/<your-agent-slug>/`.
3. Create two bus files: `task.md` (to receive assignments) and `report.md` (to send reports).

---

## 3. Reporting Your Work

When you have completed work relevant to the APM session:
1. Write your report to `.apm/bus/<your-agent-slug>/report.md`. Your first report must clearly explain who you are, what you did, and why you are participating in the session - the Manager has no record of your participation. Include enough context for the Manager to assess the situation. Subsequent reports can be concise.
2. Direct the User to deliver the report: `/apm-5-check-reports <your-agent-slug>` in the Manager's chat.

---

## 4. Receiving Follow-Up Assignments

The Manager may assign follow-up work through your Task Bus after processing your initial report:
1. The User signals you to check your bus (by referencing the Task Bus file).
2. Read `.apm/bus/<your-agent-slug>/task.md` and process the assignment.
3. Write your report to the Report Bus and direct the User to deliver it.
