---
command_name: check-tasks
description: 将 Task Prompt 交付给 APM Worker。
---

# APM {VERSION} - Worker 检查任务命令

检查当前 Worker 的 Task Bus 是否有待执行的 Task Prompt。如果你是 Planner、Manager 或非 APM agent，请简要拒绝并停止。

本命令替代手动引用文件。你需要从已注册身份或 `[agent-id]` 参数解析 bus 路径。

参数：可选 `[agent-id]`。如果当前 Worker 已注册，忽略该参数；如果未注册，则必须提供该参数来解析身份。

**流程：**

1. 判断注册状态：
   - 如果已注册，从注册信息得到 bus 路径，进入第 3 步。
   - 如果未注册，必须使用 `{ARGS}`。如果未提供参数，告知用户需要 agent-id。

2. 解析 agent-id：仅适用于未注册 Worker。按 `{SKILL_PATH:apm-communication}` 的规则解析 `{ARGS}`，并执行 `{COMMAND_PATH:apm-3-initiate-worker}` 的启动流程。

3. 读取 Task Bus：

   ```text
   .apm/bus/<agent-slug>/task.md
   ```

   - 如果为空，告知用户当前没有待处理任务。
   - 如果有内容，进入第 4 步。

4. 校验 Task Prompt YAML frontmatter 中的 `agent` 字段是否匹配当前身份。如果不匹配，说明路由错误，拒绝执行并提示用户切换到正确 Worker。如果匹配，按 `{GUIDE_PATH:task-execution}` 执行任务。

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
command_name: check-tasks
description: Deliver a Task Prompt to an APM Worker.
---

# APM {VERSION} - Worker Check Tasks Command

Check your Task Bus for pending Task Prompts. If you are a Planner, Manager, or non-APM agent, concisely decline and take no action. This command replaces manual file referencing - you resolve your bus path from your registered identity or from the provided `[agent-id]` argument.

Accepts an optional `[agent-id]` argument. If registered, ignore it (bus path already known). If not registered, the argument is required to resolve identity.

**Procedure:**
1. Determine registration state:
   - If registered, resolve bus path from registration. Continue to step 3.
   - If not registered, `{ARGS}` is required. If no argument provided, inform User that an agent-id is required.

2. Resolve agent-id (unregistered Workers only): resolve `{ARGS}` against `.apm/bus/` directory names per `{SKILL_PATH:apm-communication}` §4.2 Agent ID Resolution. Initialize per `{COMMAND_PATH:apm-3-initiate-worker}` §2 Initiation.

3. Read Task Bus at `.apm/bus/<agent-slug>/task.md`.
   - If empty, inform User that no pending Task is available. Await next invocation.
   - If content present, continue to step 4.

4. Cross-validate `agent` field in YAML frontmatter against registered identity. Mismatch flags a routing error - decline and direct User to the correct Worker. Process the Task per `{GUIDE_PATH:task-execution}` §3 Task Execution Procedure.

---

**End of Command**
