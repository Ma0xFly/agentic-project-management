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
