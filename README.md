# Agentic Project Management 中文版（APM）

[![License: MPL-2.0](https://img.shields.io/badge/License-MPL_2.0-brightgreen.svg)](https://opensource.org/licenses/MPL-2.0) [![npm](https://img.shields.io/npm/v/agentic-pm)](https://www.npmjs.com/package/agentic-pm)

*用一组 AI 助手管理复杂软件项目，让规划、执行、验收和交接保持清晰。*

## APM 是什么

APM 是一个开源的 AI 原生项目管理框架。它不是让你在一个越来越混乱的长对话里完成整个项目，而是把工作拆成多个职责清晰的 AI 会话：有人负责规划，有人负责协调，有人负责执行。

APM 的核心目标是解决高强度 AI 编码中的几个问题：

- 对话过长后，上下文窗口（Context Window）被污染或压缩；
- AI 忘记业务目标，开始凭空补全需求；
- 修复一个问题时破坏另一个功能；
- 任务边界不清，代码修改范围失控；
- 项目状态只存在聊天记录里，无法交接、回溯和恢复。

APM 通过结构化文件保存项目状态。即使某个 AI 会话达到上下文上限，也可以通过交接（Handoff）把关键工作知识转移到新的会话。

## 三类 Agent

APM 使用 3 类 Agent：

| 角色 | 职责 | 不该做什么 |
|---|---|---|
| **Planner** | 收集需求，产出 `Spec`、`Plan` 和 `Rules` | 不写代码，不直接执行实现任务 |
| **Manager** | 拆任务、分派 Worker、审查报告、维护项目状态 | 默认不亲自实现功能 |
| **Worker** | 执行一个明确任务，验证结果，写日志和报告 | 不自行扩大范围，不读取协调层文档 |

Planner 负责把项目说清楚；Manager 负责让项目不跑偏；Worker 负责小步执行。

## 一次 APM 会话是什么样

1. 你启动 Planner。
2. Planner 读取工作区、询问需求、整理理解摘要。
3. 你确认后，Planner 生成：
   - `.apm/spec.md`：要做什么；
   - `.apm/plan.md`：如何组织工作；
   - `AGENTS.md` 或项目规则文件：如何执行。
4. 你新开 Manager 会话。
5. Manager 按计划给 Worker 分派任务。
6. 你把任务交给对应 Worker。
7. Worker 执行、验证、写日志、提交报告。
8. 你把报告交回 Manager。
9. Manager 审查、更新状态，并继续派发下一个任务。

整个过程中，所有跨 Agent 通信都通过 `.apm/bus/` 中的文件进行。你是每个边界的触发者，因此可以在每一步审查和打断。

## 快速开始

安装 CLI：

```bash
npm install -g agentic-pm
```

进入你的项目目录：

```bash
cd your-project
```

初始化 APM：

```bash
apm init
```

如果你使用的是本中文分叉：

```bash
apm custom -r <你的用户名>/<你的仓库名>
```

选择 AI 助手后，APM 会把命令、指南、技能和项目模板安装到当前工作区。

Codex CLI 不支持自定义 slash command，因此 APM 命令会以 skill 形式安装，使用 `$` 前缀调用，例如：

```text
$apm-1-initiate-planner
```

Claude Code、Cursor 等支持 slash command 的工具通常使用：

```text
/apm-1-initiate-planner
```

你也可以在命令后附加项目背景：

```text
$apm-1-initiate-planner 我想开发一个内部工单系统，重点是权限、流程审批和移动端适配。
```

## CLI 命令

| 命令 | 作用 |
|---|---|
| `apm init` | 从官方 release 初始化 |
| `apm custom` | 从自定义仓库安装，例如你的中文分叉 |
| `apm update` | 更新已安装的模板 |
| `apm archive` | 归档当前 APM 会话 |
| `apm add` / `apm remove` | 添加或移除 AI 助手配置 |
| `apm status` | 查看当前安装状态 |

## APM 命令速查

| 命令 | 中文含义 | 使用时机 |
|---|---|---|
| `apm-1-initiate-planner` | 启动 Planner | 新项目或大功能开始前 |
| `apm-2-initiate-manager` | 启动 Manager | 规划文档批准后 |
| `apm-3-initiate-worker` | 启动 Worker | Manager 已分派任务后 |
| `apm-4-check-tasks` | Worker 检查任务 | 把 Manager 的任务交给 Worker |
| `apm-5-check-reports` | Manager 检查报告 | Worker 完成任务后 |
| `apm-6-handoff-manager` | Manager 交接 | Manager 上下文接近上限时 |
| `apm-7-handoff-worker` | Worker 交接 | Worker 上下文接近上限时 |
| `apm-8-summarize-session` | 总结会话 | 阶段结束或归档前 |
| `apm-9-recover` | 恢复上下文 | 会话压缩、丢失或中断后 |

## 中文版改造说明

本仓库是面向中文使用者的 APM 本土化分叉。目标不是把上游英文文档压缩成中文摘要，而是在中文可读性和原项目约束强度之间取得平衡。

运行时模板采用“双层结构”：

- **中文执行层**：用中文解释角色、流程、关键动作和常见误区，方便你快速判断当前 APM 应该做什么。
- **原版契约层**：保留上游 APM 原版完整行为契约，维持 Planner、Manager、Worker 的流程约束、上下文边界、审批门槛、依赖判定、日志格式、交接机制和验证标准。

这样做会让 release zip 比压缩版更大，属于正常现象。APM 的“性能”主要来自这些高密度流程约束，而不是文件体积越小越好。后续维护时不要为了减小 zip 体积删除原版契约层。

本分叉优先汉化 Agent 运行时会读取的内容：

- 命令模板；
- 工作指南；
- 通信规则；
- `.apm` 项目产物模板；
- APM Assist 和自定义说明。

暂不修改 CLI 安装和更新逻辑。推荐继续使用官方 `agentic-pm` CLI，通过 `apm custom -r Ma0xFly/agentic-project-management` 安装本中文分叉的 release。

## 自定义和发布

修改模板后运行：

```bash
npm run build:release
```

构建产物会输出到 `dist/`。发布 GitHub Release 时，需要上传：

- `apm-release.json`
- 对应助手的 zip bundle，例如 `codex.zip`

然后在目标项目中安装：

```bash
apm custom -r Ma0xFly/agentic-project-management -t <tag>
```

## 许可证

本项目继承上游许可证 **Mozilla Public License 2.0 (MPL-2.0)**。核心文件的修改需要遵守 MPL-2.0 要求。
