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

