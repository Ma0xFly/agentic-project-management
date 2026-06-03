---
name: apm-assist
description: 中文 APM 助手。用于解释 APM、检查安装状态、回答使用问题、辅助迁移和排查。
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
name: apm-assist
description: Reference and procedures for helping users understand APM, detect their installation state, answer questions from live docs, and handle migration from older versions.
---

# APM Assist

## 1. Overview

This skill provides the context and procedures needed to help users with APM (Agentic Project Management). It covers explaining concepts, detecting installation state, answering questions from the live documentation, and guiding migration from older versions.

**This skill is not part of the APM workflow.** APM sessions run in their own dedicated conversations - the Planner, Manager, and Workers each get a separate chat. This skill is for answering questions about APM and helping with setup or migration. If the user wants to start an APM session, fetch the [Getting Started](https://agentic-project-management.dev/docs/getting-started) guide and walk them through the steps it describes. Do not attempt to run APM procedures in this conversation.

### 1.1 What This Skill Covers

- **Explaining APM** - How APM works, its concepts, workflow, and architecture
- **Detecting state** - Reading `.apm/metadata.json` to identify the installed version, source, and assistants
- **Answering questions** - Fetching live documentation pages to give accurate, up-to-date answers
- **Migration** - Guiding users from older APM versions to the current release (see §5)

### 1.2 How to Use This Skill

- Fetch the relevant documentation page before answering questions. Training data may be outdated.
- Be direct and practical. Users asking about APM want actionable information, not theory.
- When a question maps to a specific doc page, summarize the relevant section and link to the full page.
- When the user has an active APM installation, ground answers in their actual project state.

---

## 2. APM at a Glance

APM (Agentic Project Management) is a framework for building complex software projects with AI assistants. Instead of one long chat that degrades as context fills, APM structures work into a coordinated system with three Agent types:

- **Planner** - Runs once at project start. Gathers requirements through structured discovery, then creates three planning documents: the Spec (what to build), the Plan (how work is organized into Stages and Tasks), and the Rules (how work is performed).
- **Manager** - Coordinates execution. Assigns Tasks to Workers via self-contained Task Prompts, reviews completed work, and maintains project state.
- **Workers** - Execute Tasks in specific domains (frontend, backend, etc.). Each Worker sees only its Task Prompt and the Rules, not the full project scope.

Agents communicate through a file-based Message Bus in `.apm/bus/`. The user carries messages between Agent conversations by running commands. Project state lives in structured files (`.apm/`) so nothing is lost when a conversation ends.

When an Agent's context fills, a Handoff transfers working knowledge to a fresh instance. Sessions can be archived and built upon by future Planners.

APM is installed via the `agentic-pm` CLI (`npm install -g agentic-pm`) and supports Claude Code, Cursor, GitHub Copilot, Gemini CLI, OpenCode, and Codex.

---

## 3. Documentation Reference

The official documentation is the source of truth. Always fetch the relevant page rather than relying on the overview above or on training data.

**LLM entry point:** https://agentic-project-management.dev/llms.txt - structured index of all docs with descriptions, follows the llms.txt standard.

**Documentation site:** https://agentic-project-management.dev/docs

| Page | URL path | Covers |
| :--- | :--- | :--- |
| Introduction | `/docs/introduction` | What APM is, the problem it solves, high-level overview |
| Getting Started | `/docs/getting-started` | Installation, first session walkthrough, all steps |
| Quick Reference | `/docs/quick-reference` | Commands, CLI, file structure, workflow cheat sheet |
| Agent Types | `/docs/agent-types` | Planner, Manager, Worker roles and responsibilities |
| Agent Orchestration | `/docs/agent-orchestration` | Planning documents, Message Bus, Task Prompts, Memory, Handoff |
| Workflow Overview | `/docs/workflow-overview` | Every procedure in both phases, step by step |
| Prompt Engineering | `/docs/prompt-engineering` | How APM's files are designed and structured |
| Context Engineering | `/docs/context-engineering` | How Agent context is scoped and why |
| CLI Guide | `/docs/cli` | All CLI commands, options, directory structure, metadata schema |
| Troubleshooting | `/docs/troubleshooting-guide` | Common issues, recovery procedures, migration |
| Customization Guide | `/docs/customization-guide` | Custom repositories, build pipeline, template modification |
| Security Guide | `/docs/security` | Trust model, custom bundle risks, mitigation |
| Tips and Tricks | `/docs/tips-and-tricks` | Model selection, cost optimization, workflow efficiency |

**Repository:** https://github.com/sdi2200262/agentic-project-management

The full documentation is also available as a single file:
`https://agentic-project-management.dev/llms-full.txt`

When the documentation site is unreachable, fetch docs directly from the website repository:
`https://raw.githubusercontent.com/sdi2200262/apm-website/main/docs/<filename>.md`

---

## 4. Version Detection

When the user asks about their APM installation, or when knowing the setup would help answer a question:

1. **Check for `.apm/metadata.json`** - If it exists, read it. Key fields:
   - `source`: `"official"` or `"custom"`
   - `repository`: e.g. `"sdi2200262/agentic-project-management"`
   - `releaseVersion`: e.g. `"v1.0.0"`
   - `cliVersion`: CLI version that performed the install
   - `assistants`: Array of installed assistant IDs

2. **Check for old metadata formats** - If `metadata.json` exists but uses `templateVersion` instead of `releaseVersion`, or stores assistant names as full display names instead of short IDs, this is a pre-v1.0.0 installation. See §5 for migration.

3. **Check CLI version** - `apm --version` or `npm list -g agentic-pm` shows the installed CLI version.

4. **No `.apm/` directory** - APM is not initialized. The user can run `apm init` after installing the CLI.

---

## 5. Migration

When a pre-v1.0.0 installation is detected, or when the user asks about migration, follow this procedure. The goal is to preserve existing work as an archive and leave the project ready for the current CLI.

### 5.1 Assess

1. Read `.apm/metadata.json` to determine the installed version and configured assistants
2. Check which CLI version is installed (`apm --version` or `npm list -g agentic-pm`)
3. Fetch the current CLI Guide from the documentation to understand the expected metadata schema and directory structure
4. List what exists in `.apm/` and in assistant directories (`.claude/`, `.cursor/`, etc.)

### 5.2 Explain

Present findings to the user:
- Which version is installed (CLI and templates)
- What active session artifacts exist
- Key differences from the current release
- What the migration will involve

### 5.3 v0.5.x to v1.0.0 Migration Map

This section provides the specific mappings between v0.5.x and v1.0.0. Always cross-reference against the current documentation as well.

**Agent roles:**

| v0.5.x | v1.0.0 | Notes |
| :--- | :--- | :--- |
| Setup Agent | Planner | Same role (discovery + planning), renamed |
| Manager Agent | Manager | Unchanged |
| Implementation Agent | Worker | Same role (task execution), renamed. Workers now have identifiers (e.g. `frontend-agent`) |
| Ad-Hoc Agent | (removed) | Subagent spawning is now native to Planner, Manager, and Workers |

**Commands:**

| v0.5.x | v1.0.0 |
| :--- | :--- |
| `/apm-1-initiate-setup` | `/apm-1-initiate-planner` |
| `/apm-2-initiate-manager` | `/apm-2-initiate-manager` |
| `/apm-3-initiate-implementation` | `/apm-3-initiate-worker <id>` |
| `/apm-4-delegate` | (removed - native subagents) |
| `/apm-5-handover-manager` | `/apm-6-handoff-manager` |
| `/apm-5-handover-implementation` | `/apm-7-handoff-worker` |
| (none) | `/apm-4-check-tasks` (Message Bus delivery) |
| (none) | `/apm-5-check-reports` (Message Bus delivery) |
| (none) | `/apm-8-summarize-session` |
| (none) | `/apm-9-recover` |

**File structure:**

| v0.5.x | v1.0.0 |
| :--- | :--- |
| `.apm/Implementation_Plan.md` | `.apm/spec.md` + `.apm/plan.md` (split into two) |
| `.apm/Memory/Memory_Root.md` | `.apm/memory/index.md` |
| `.apm/Memory/<phase>/<task>.md` | `.apm/memory/stage-NN/task-NN-NN.log.md` |
| `.apm/guides/` | Platform directory (e.g. `.claude/apm-guides/`) |
| (none) | `.apm/tracker.md` (live project state) |
| (none) | `.apm/bus/` (file-based Message Bus) |

**Metadata (v0.5.x):** Two formats exist depending on the minor version:

Early v0.5.x (single-assistant):
```json
{ "version": "v0.5.0", "assistant": "Cursor", "installedAt": "..." }
```

Later v0.5.x (multi-assistant, auto-migrated by CLI):
```json
{
  "cliVersion": "0.5.4",
  "templateVersion": "v0.5.4+templates.2",
  "assistants": ["Cursor", "Claude Code"],
  "installedAt": "...",
  "lastUpdated": "..."
}
```

Key differences from v1.0.0 metadata: v0.5.x uses `templateVersion` (with `+templates.N` build suffix) instead of `releaseVersion`, stores assistant names as full display names (`"Claude Code"`) instead of short IDs (`"claude"`), has no `source`, `repository`, or `installedFiles` fields.

**Metadata (v1.0.0):**
```json
{
  "source": "official",
  "repository": "sdi2200262/agentic-project-management",
  "releaseVersion": "v1.0.0",
  "cliVersion": "1.0.0",
  "assistants": ["cursor", "claude"],
  "installedFiles": { "cursor": ["..."], "claude": ["..."] },
  "installedAt": "..."
}
```

Archive metadata adds `archivedAt` and `reason` (e.g. `"migration"`) fields.

**Guides and installed files:** In v0.5.x, procedural guides lived in `.apm/guides/`. In v1.0.0, guides, skills, agent configs, and commands are all installed into the platform-specific directory (e.g. `.claude/apm-guides/`, `.claude/skills/`, `.claude/agents/`, `.claude/commands/`). The `.apm/` directory in v1.0.0 contains only project artifacts (Spec, Plan, Tracker, Memory, Bus, metadata).

**Archives:** v0.5.x `apm update` created backup directories at `.apm/apm-backup-<tag>/` with optional zip files - no structured archive metadata. v1.0.0 uses `.apm/archives/session-YYYY-MM-DD-NNN/` with proper `metadata.json` inside each archive. During migration, old backup directories should be consolidated into the v1.0.0 archive format.

**Workflow changes:**
- Context Synthesis (4 question rounds) became Context Gathering (3 rounds)
- Project Breakdown became Work Breakdown, now produces Spec + Plan + Rules instead of a single Implementation Plan
- Task delivery changed from copy-paste (code blocks) to file-based Message Bus
- Ad-Hoc delegation replaced by native subagent spawning
- Enhancement phase and AI Review phase removed

**CLI changes:** v0.5.x CLI had only `apm init` and `apm update`. v1.0.0 adds `apm archive`, `apm custom`, `apm add`, `apm remove`, and `apm status`. The `apm init` command in v1.0.0 is fresh-install only (refuses to run if already initialized).

**Platform support:** v0.5.x supported 11 assistants (including Windsurf, Kilo Code, Roo Code, Auggie CLI, Google Antigravity, Qwen Code). v1.0.0 narrowed to Cursor, Claude Code, GitHub Copilot, Gemini CLI, OpenCode, and Codex.

### 5.4 Execute

**Step 1 - Archive.** Move existing `.apm/` artifacts into `.apm/archives/session-YYYY-MM-DD-001/`. Convert old `metadata.json` to the current archive schema (fetch the CLI Guide for the current schema). Create an archive index at `.apm/archives/index.md`.

**Step 2 (optional) - Session summary.** If the user wants, generate a `session-summary.md` from the old artifacts. Include a Migration Notice at the top stating the old version and terminology differences. Adapt sections to whatever the old artifacts contain.

**Step 3 - Clean assistant directories.** Remove only APM-installed files from assistant directories. List every file before deleting. Do NOT remove non-APM content. When uncertain, ask.

**Step 4 - Clean metadata.** Remove `.apm/metadata.json` from the root so the current CLI sees a clean state.

### 5.5 Next Steps

After migration, the user should:
1. Update the CLI: `npm install -g agentic-pm`
2. Initialize: `apm init`
3. The new Planner will detect the migration archive during Context Gathering

---

## 6. Answering Questions

When the user asks a question about APM:

1. **Identify which doc page covers it** using the table in §3
2. **Fetch that page** from the documentation site or repository
3. **Answer based on the fetched content** - summarize the relevant section, quote key details
4. **Link to the full page** so the user can read more

If the question spans multiple pages, fetch the most relevant one and reference the others. If the question is about the user's specific project, also check their `.apm/` artifacts for context.

For questions about models, cost, or workflow tips - fetch Tips and Tricks.
For questions about commands or file structure - fetch CLI Guide or Quick Reference.
For questions about errors or unexpected behavior - fetch Troubleshooting Guide.

---

**End of Skill**
