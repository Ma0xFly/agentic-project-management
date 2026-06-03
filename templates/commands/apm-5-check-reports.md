---
command_name: check-reports
description: 将 Task Report 交付给 APM Manager。
---

# APM {VERSION} - Manager 检查报告命令

检查 Report Bus 中是否有 Worker 提交的 Task Report。如果你是 Planner、Worker 或非 APM agent，请简要拒绝并停止。

参数：可选 `[agent-id ...]`。如果提供参数，只检查指定 Workers 的 Report Bus；如果不提供参数，扫描所有 Report Bus。

**流程：**

1. 判断扫描范围：
   - 如果 `{ARGS}` 有内容，按 `{SKILL_PATH:apm-communication}` 解析每个 agent-id，并批量读取对应 Report Bus。
   - 如果没有参数，进入第 2 步。

2. 扫描所有 Report Bus。使用一次终端命令读取所有非空文件，例如：

   ```bash
   for f in .apm/bus/*/report.md; do [ -s "$f" ] && echo "=== $f ===" && cat "$f"; done
   ```

   在 Windows PowerShell 中使用等价命令。如果没有待处理报告，告知用户并等待下一次调用。

3. 对每个有内容的 Report Bus，按 `{GUIDE_PATH:task-review}` 执行 Task Review。

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
command_name: check-reports
description: Deliver a Task Report to an APM Manager.
---

# APM {VERSION} - Manager Check Reports Command

Check Report Bus(es) for pending Task Reports. If you are a Planner, Worker, or non-APM agent, concisely decline and take no action. This replaces manual file referencing - scan bus directories or check a specific Worker's Report Bus.

Accepts optional `[agent-id ...]` arguments. With arguments, checks those Workers' Report Buses. Without arguments, checks Workers with active dispatches plus a health check for unexpected content.

**Procedure:**
1. Determine scan scope:
   - If `{ARGS}` provided, resolve each agent-id per `{SKILL_PATH:apm-communication}` §4.2 Agent ID Resolution. Batch-read all targeted Report Buses in a single terminal invocation, e.g., `cat .apm/bus/<slug-1>/report.md .apm/bus/<slug-2>/report.md` (or platform equivalent). Continue to step 3.
   - If no argument, continue to step 2.

2. Scan Report Buses: scan and read all Report Buses in a single terminal invocation, e.g., `for f in .apm/bus/*/report.md; do [ -s "$f" ] && echo "=== $f ===" && cat "$f"; done` (or platform equivalent). This discovers non-empty buses, reads their content, and includes path markers for cross-referencing against active dispatches. If any unexpected bus has content (beyond the actively dispatched Workers), include it and inform the User. If no buses have content, inform User that no pending reports are available. Await next invocation. If one or more have content, continue to step 3 for each.

3. Process report(s): for each Report Bus with content, process per `{GUIDE_PATH:task-review}` §3 Task Review Procedure.

---

**End of Command**
