---
name: apm-customization
description: 指导 AI 助手定制 APM 模板、构建 release，并维护自定义 APM 仓库。
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
# APM Customization Skill 中文版

## 1. 目标

本 skill 用于在 APM fork 或 template 仓库中进行定制。适用场景包括：

- 汉化 APM 模板；
- 修改命令、guides、skills；
- 调整 Agent 行为；
- 构建自定义 release；
- 通过 `apm custom` 安装自定义仓库。

## 2. 仓库结构

重点目录：

- `templates/`：安装包源模板。`apm init` 或 `apm custom` 最终安装的内容来自这里。
- `templates/commands/`：用户触发的命令入口。
- `templates/guides/`：Agent 执行时读取的流程指南。
- `templates/skills/`：多个角色共享的能力说明。
- `templates/agents/`：subagent 配置。
- `templates/apm/`：`.apm/` 项目产物模板。
- `templates/_standards/`：开发期规范，不进入 bundle。
- `build/`：构建系统。
- `src/`：`agentic-pm` CLI 源码。
- `skills/`：独立 skills，不属于主 bundle。

## 3. 修改原则

优先修改 `templates/`，不要轻易改 `src/`。

原因：

- `src/` 影响 CLI 本体；
- `templates/` 影响安装后的 Agent 行为；
- 中文化主要是模板层需求。

必须保留：

- frontmatter 字段；
- 命令文件名；
- skill 名称；
- `{VERSION}`、`{GUIDE_PATH:*}`、`{SKILL_PATH:*}`、`{COMMAND_PATH:*}`、`{ARGS}` 等占位符；
- bundle 目录结构。

## 4. 构建

安装依赖：

```bash
npm install
```

构建 release：

```bash
npm run build:release
```

产物在：

```text
dist/
```

通常包含：

- `apm-release.json`
- `codex.zip`
- `claude.zip`
- `cursor.zip`
- 其他平台 zip

## 5. 发布

1. 提交修改；
2. 创建 tag，例如 `v1.0.1-cn.1`；
3. 在 GitHub 创建 Release；
4. 上传 `dist/` 下所有文件；
5. 在目标项目安装：

```bash
apm custom -r <owner>/<repo> -t v1.0.1-cn.1
```

## 6. 验证

发布前至少验证：

- `npm run build:release` 成功；
- `dist/apm-release.json` 存在；
- 目标 zip 中包含中文模板；
- 在测试项目中执行 `apm custom -r <owner>/<repo> -t <tag>`；
- Codex CLI 下能看到 `.agents/skills/apm-*`；
- `$apm-1-initiate-planner` 能正常启动。

## 7. 说明自定义差异

中文分叉 README 应说明：

- 改了哪些模板；
- 是否改变流程；
- 如何安装；
- 如何同步上游；
- 用户需要信任自定义 bundle 会写入项目文件。

---

**Skill 结束**
