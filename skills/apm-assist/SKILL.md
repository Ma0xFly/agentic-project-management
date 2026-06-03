---
name: apm-assist
description: 中文 APM 助手。用于解释 APM、检查安装状态、回答使用问题、辅助迁移和排查。
---

## 0. 纯中文本土化执行规范

本文件是 APM 中文本土化版本。执行时必须遵守以下规则：

- 本文件的中文说明就是实际执行口径，不需要再参考英文原文。
- 面向用户的解释、提问、分析、总结、风险说明、审查意见和下一步指令必须使用中文。
- APM 项目产物正文必须使用中文，包括 `.apm/spec.md`、`.apm/plan.md`、`{RULES_FILE}` 中的 APM 规则、Task Prompt、Task Log、Task Report、Handoff Log、Recovery Summary、Stage Summary、Memory Notes 和 Working Notes。
- 可以保留英文的内容仅限命令、路径、代码标识、YAML 字段、Markdown 结构标题、状态值、Agent 名称、Task ID、mermaid 语法、协议字段、库名、框架名和行业通用缩写。
- 不得为了节省上下文而删除流程约束。必须保留审批门槛、上下文边界、依赖判定、验证标准、日志格式、Message Bus、Handoff、Recovery、Tracker 和 Memory 相关规则。
- 如果发现规则缺口，用中文补足；不要回退到英文说明。

---
# APM Assist 中文版

## 1. 目标

本 skill 用于帮助用户理解和使用 APM。它不是 APM 正式工作流的一部分，不扮演 Planner、Manager 或 Worker。

如果用户要启动 APM 项目，不要在当前对话里模拟完整 APM 流程；应指导用户在独立会话中运行对应命令。

## 2. 可回答的问题

- APM 是什么；
- Planner、Manager、Worker 分别做什么；
- `apm init`、`apm custom`、`apm update`、`apm archive` 的区别；
- 当前项目是否已初始化 APM；
- 当前安装来自官方还是自定义仓库；
- Codex CLI 为什么使用 `$apm-*` 而不是 slash command；
- 如何 Handoff、Recover、Summarize 和 Archive；
- 如何基于中文分叉安装。

## 3. 回答原则

- 优先用中文回答；
- 优先检查当前项目实际文件，而不是泛泛解释；
- 涉及当前安装状态时，读取 `.apm/metadata.json`；
- 涉及具体流程时，优先读取当前安装的命令、guides 和 skills；
- 需要官方最新信息时，再查官方文档或仓库。

## 4. 安装状态检查

当用户问本项目 APM 状态时：

1. 检查 `.apm/metadata.json`。
2. 读取关键字段：
   - `source`：`official` 或 `custom`；
   - `repository`：来源仓库；
   - `releaseVersion`：模板 release；
   - `cliVersion`：安装时 CLI 版本；
   - `assistants`：已安装助手。
3. 检查当前 assistant 目录，例如：
   - Codex CLI：`.agents/skills/`
   - Claude Code：`.claude/commands/` 和 `.claude/skills/`
   - Cursor：`.cursor/commands/` 和 `.cursor/skills/`

如果没有 `.apm/`，说明当前目录未初始化。

## 5. 常用解释

### APM 的核心

APM 把一个长对话拆成多个职责明确的会话：

- Planner 负责需求和规划；
- Manager 负责协调和验收；
- Worker 负责具体执行。

项目状态保存在 `.apm/`，Agent 通过 `.apm/bus/` 交换任务和报告。

### Codex CLI 命令

Codex CLI 不支持自定义 slash command，所以 APM 命令以 skills 形式安装，用 `$` 前缀调用：

```text
$apm-1-initiate-planner
$apm-2-initiate-manager
$apm-3-initiate-worker <agent-id>
```

### 自定义中文分叉安装

```bash
apm custom -r <owner>/<repo>
```

指定 release：

```bash
apm custom -r <owner>/<repo> -t <tag>
```

## 6. 迁移旧版本

如果检测到旧 metadata，例如 `templateVersion` 而不是 `releaseVersion`，说明可能是 v0.5.x。

建议流程：

1. 先备份当前 `.apm/`；
2. 生成会话总结；
3. 清理旧 APM 安装文件；
4. 重新运行 `apm init` 或 `apm custom`；
5. 新 Planner 在 Context Gathering 阶段读取旧归档。

不要直接删除用户文件。列出待删除内容并等待确认。

---

**Skill 结束**
