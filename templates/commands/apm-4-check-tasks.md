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

