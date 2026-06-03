# APM 中文重构计划

本文件记录中文化改造范围，避免把 APM 改成不可构建或不可更新的分叉。

## 改造原则

- 保留命令 ID、frontmatter 字段、占位符和构建脚本。
- 优先汉化 Agent 运行时会读取的模板，而不是机械翻译所有仓库文件。
- 中文表达以可执行、可审查、可交接为目标。
- 术语首次出现时保留英文对照，例如上下文窗口（Context Window）。

## 第一阶段范围

- `README.md`
- `skills/README.md`
- `skills/apm-assist/SKILL.md`
- `skills/apm-customization/SKILL.md`
- `templates/commands/*.md`
- `templates/guides/*.md`
- `templates/skills/apm-communication/*.md`
- `templates/apm/*.md`

## 暂不修改

- `src/` CLI 源码
- `build/` 构建脚本
- `package.json` 包名和版本

