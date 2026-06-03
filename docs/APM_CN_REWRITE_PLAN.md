# APM 中文本土化维护规范

本文件记录中文本土化改造范围，避免把 APM 改成不可构建、不可更新，或行为约束明显弱于上游的分叉。

## 改造原则

- 保留命令 ID、frontmatter 字段、占位符和构建脚本。
- 优先汉化 Agent 运行时会读取的模板，而不是机械翻译所有仓库文件。
- 中文表达以可执行、可审查、可交接为目标。
- 术语首次出现时保留英文对照，例如上下文窗口（Context Window）。
- 不做压缩精简版。APM 的流程质量依赖高密度约束、审批门槛、上下文边界、依赖分类、验证标准和交接机制。
- 运行时模板采用“双层结构”：中文执行层负责可读性，原版契约层负责完整行为约束。
- 如果中文执行层与原版契约层存在理解差异，维护时应采用更严格、更具体、更能约束 Agent 行为的表达。

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

- 不删除原版行为契约层。
- 不删除审批 gate、报告格式、日志格式、dependency graph、Handoff、Recovery、Message Bus 等流程约束。
- 不把 “Validation” 写成“确认无误”“功能正常”这类不可执行描述。
- 不让 Worker 直接读取 Spec、Plan、Tracker 或 Index；Worker 的上下文边界必须保持。
- 不把 Manager 的运行时协调职责提前写死到 Planner 的 Plan 中。

## 暂不修改

- `src/` CLI 源码
- `build/` 构建脚本
- `package.json` 包名和版本
