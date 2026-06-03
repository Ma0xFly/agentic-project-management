# APM {VERSION} - Task Logging Guide

## 1. 适用角色

**Reading Agent:** Worker

本指南定义 Worker 如何写 Task Log 和 Task Report。Task Log 保存任务级事实，Task Report 是给 Manager 的简短交付消息。

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

