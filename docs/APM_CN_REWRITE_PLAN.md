# APM 中文本土化维护规范

本文件记录中文本土化改造范围，避免把 APM 改成不可构建、不可更新，或行为约束明显弱于上游的分叉。

## 改造原则

- 保留命令 ID、frontmatter 字段、占位符和构建脚本。
- 优先汉化 Agent 运行时会读取的模板，而不是机械翻译所有仓库文件。
- 中文表达以可执行、可审查、可交接为目标。
- 术语首次出现时保留英文对照，例如上下文窗口（Context Window）。
- 不做压缩精简版。APM 的流程质量依赖高密度约束、审批门槛、上下文边界、依赖分类、验证标准和交接机制。
- 运行时模板采用纯中文本土化，不保留英文原版契约层，避免占用上下文并诱导英文产物。
- 维护时应把上游英文规则的精华吸收为中文规则：职责边界、流程 gate、上下文隔离、Message Bus、Tracker、Memory、Handoff、Recovery、验证和审查都必须保留。
- 面向用户的对话、`.apm/spec.md`、`.apm/plan.md`、`Rules`、Task Prompt、Task Log、Task Report、Handoff Log、Recovery Summary、Stage Summary、Memory Notes 和 Working Notes 的正文必须中文。

## 已本土化范围

- `README.md`
- `skills/README.md`
- `skills/apm-assist/SKILL.md`
- `skills/apm-customization/SKILL.md`
- `templates/commands/*.md`
- `templates/guides/*.md`
- `templates/skills/apm-communication/*.md`
- `templates/apm/*.md`

## 运行时模板维护要求

以下文件会进入 release bundle，直接影响 Planner、Manager、Worker 行为：

- `templates/commands/*.md`
- `templates/guides/*.md`
- `templates/skills/apm-communication/*.md`
- `templates/agents/*.md`
- `templates/apm/*.md`

维护这些文件时必须遵守：

- 不恢复双语契约层。
- 不删除审批 gate、报告格式、日志格式、dependency graph、Handoff、Recovery、Message Bus 等流程约束。
- 不把 “Validation” 写成“确认无误”“功能正常”这类不可执行描述。
- 不让 Worker 直接读取 Spec、Plan、Tracker 或 Index；Worker 的上下文边界必须保持。
- 不把 Manager 的运行时协调职责提前写死到 Planner 的 Plan 中。
- 不把英文说明塞回运行时模板；需要补规则时，用中文重写。

## 暂不修改

- `src/` CLI 源码
- `build/` 构建脚本
- `package.json` 包名和版本
