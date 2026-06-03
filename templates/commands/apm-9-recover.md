---
command_name: recover
description: 恢复 APM agent 的工作上下文。
---

# APM {VERSION} - Recover 命令

本命令用于在平台自动压缩、手动压缩、会话丢失或中断后重建工作上下文。仅适用于 Manager 和 Worker。如果你是 Planner 或非 APM agent，请简要拒绝并停止。

参数：可选 `[role]`，可以是 `manager` 或 Worker agent identifier，例如 `frontend-agent`。

当前参数：`{ARGS}`。

**中文恢复规则：**

- 恢复过程中面向用户的说明、问题、当前状态判断和下一步动作必须使用中文。
- 写入 `Tracker` 的 recovery working note 必须中文。
- Worker 在下一份 Task Report 中说明恢复事件时必须中文。
- 引用英文命令输出或错误消息时，可以保留原文，但必须补充中文解释。

**流程：**

1. 判断角色：
   - 如果提供参数，`manager` 表示 Manager，其他值按 Worker agent-id 处理。
   - 如果未提供参数，从当前会话上下文或压缩摘要判断。
   - 如果无法判断，询问用户。

2. 重新读取对应启动命令及其引用的所有文档：
   - Manager：`{COMMAND_PATH:apm-2-initiate-manager}`
   - Worker：`{COMMAND_PATH:apm-3-initiate-worker}`

   只读启动命令不够，必须读取它引用的指南、skill 和项目产物。跳过问候和身份注册中的重复说明。

3. 从项目产物和当前代码状态恢复工作进度。如果产物无法填补关键空白，向用户询问最小必要上下文。

4. 记录恢复事件：
   - 如果你是 Manager，在 `Tracker` 中添加 working note。
   - 如果你是 Worker，在下一份 Task Report 中说明恢复事件。

   Recover 不增加 instance number，仍然作为同一个实例继续工作。

5. 恢复职责并继续。

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
command_name: recover
description: Recover an APM agent's working context.
---

# APM {VERSION} - Recover Command

This command reconstructs working context after platform auto-compaction, manual compaction, or when an initiated agent must resume after a cleared or lost conversation. It applies to the Manager and Workers only. If you are a Planner or non-APM agent, concisely decline and take no action. Accepts an optional `[role]` argument: `manager` or a Worker agent identifier (e.g., `frontend-agent`).

The command argument - if provided - will be listed here: `{ARGS}`. If empty, then no argument was provided.

**Procedure:**
1. Determine role: from the command argument if provided (`manager` for the Manager, any other value for a Worker). If no argument, infer from conversation context (compaction summary or the initiation just performed). If conversation context does not reveal your role, ask the User.
2. Re-read your initiation command and read every document it references - every guide, skill, and project artifact listed in its loading sequence:
   - Manager: `{COMMAND_PATH:apm-2-initiate-manager}`
   - Worker: `{COMMAND_PATH:apm-3-initiate-worker}`
   Reading the initiation command alone is not sufficient - the documents it references contain the procedural knowledge and project state needed for recovery. Skip identity determination and greeting.
3. Explore project state from the artifacts listed in your initiation command and the current state of the codebase to reconstruct where work stands. When gaps remain that artifacts cannot fill, ask the User for brief context before continuing.
4. Note the recovery event: if you are the Manager, add a working note to the Tracker; if you are a Worker, include it in the next Task Report. Recovery does not increment the instance number - you continue as the same instance. When eventually performing Handoff, note which portions of working context are reconstructed rather than first-hand.
5. Continue with duties.

---

**End of Command**
