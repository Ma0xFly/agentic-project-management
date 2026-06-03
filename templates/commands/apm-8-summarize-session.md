---
command_name: summarize-session
description: 总结并可选归档 APM 会话。
---

# APM {VERSION} - Summarize Session 命令

本命令用于总结当前 APM 会话，并可选择归档。你是独立总结 agent，不是 Planner、Manager 或 Worker。如果你正处于这些角色中，请简要拒绝并停止。

## 流程

1. 读取以下产物：
   - `.apm/spec.md`
   - `.apm/plan.md`
   - `.apm/tracker.md`
   - `.apm/memory/index.md`

   如果 working notes 或 Stage summaries 引用了特定 Task Logs 且需要验证，读取对应日志。不要无差别读取所有 Task Logs。

2. {SUBAGENT_GUIDANCE} 要求它读取 `.apm/` 下所有产物，包括 `.apm/memory/` 中的 Task Logs，并和当前代码库交叉验证。它需要报告：
   - 每个 Stage 的结果；
   - deliverables 是否实际存在；
   - commit、branch 和验证结果是否与 `.apm/` 状态一致；
   - 代码库是否已经偏离规划文档。

3. 写入：

   ```text
   .apm/session-summary.md
   ```

   摘要必须明确说明它是某一时间点的快照。

4. 向用户展示摘要，询问是否需要补充、修正或强调。用户反馈后更新摘要。

5. 询问是否归档当前会话。如果用户同意，询问是否使用自定义归档名。

6. 运行：

   ```bash
   apm archive --force
   ```

   或：

   ```bash
   apm archive --force --name <name>
   ```

7. 读取或创建：

   ```text
   .apm/archives/index.md
   ```

   将本次归档追加到索引表顶部。

8. 用中文告知用户归档完成。若要开始新 APM 会话，需要在终端运行 `apm init` 或 `apm custom`，然后在 AI 助手中启动 Planner。

## Session Summary 结构

位置：

```text
.apm/session-summary.md
```

YAML frontmatter：

```yaml
---
date: <YYYY-MM-DDTHH:MM:SSZ>
project: <project name>
stages_completed: <number>
total_tasks: <number>
outcome: <complete | partial | incomplete>
---
```

正文必须包含：

- **Project Scope**：项目目标和范围；
- **Stages and Outcomes**：每个 Stage 的目标、完成情况和结果；
- **Key Deliverables**：主要交付物和路径；
- **Codebase State**：实际代码库与计划产物的差异；
- **Notable Findings**：执行中形成的重要经验、修复、恢复事件和协调洞察；
- **Known Issues**：未解决问题、开放问题和注意事项；
- **Snapshot Notice**：说明该总结反映的是创建时间点的状态，之后代码库可能已变化。

## Archive Index 格式

位置：

```text
.apm/archives/index.md
```

```markdown
# APM Archive Index

| Archive | Date | Scope | Stages | Tasks |
|---------|------|-------|--------|-------|
```

最新归档放在表格顶部。

---

**命令结束**
---

## 中文本土化与原版契约保留说明

本文件采用“双层结构”：上半部分是面向中文使用者的本土化执行说明，下半部分保留上游 APM 原版完整行为契约。

执行时必须遵守以下规则：

- 中文部分用于快速理解角色、流程和关键动作，不能替代后文的原版完整契约。
- 如果中文说明没有覆盖某个细节，必须继续遵守后文原版契约中的对应规则。
- 如果中文说明与原版契约存在理解差异，采用更严格、更具体、更能约束 Agent 行为的规则。
- 不得因为已经阅读中文说明而跳过后文的审批门槛、上下文边界、依赖判定、日志格式、交接流程或验证标准。

<!-- APM_CN_ORIGINAL_CONTRACT_START -->

# Original APM Behavioral Contract (Preserved)

以下为上游 APM 原版内容，保留用于维持原项目的完整流程约束、Agent 行为边界和可回溯性。
---
command_name: summarize-session
description: Summarize and optionally archive an APM session.
---

# APM {VERSION} - Summarize Session Command

This command summarizes the current APM session and optionally archives it. You are a standalone agent - not a Planner, Manager, or Worker. If you are one of those roles, concisely decline and take no action.

**Procedure:**
1. Read the following artifacts directly (these reads are independent):
   - `.apm/spec.md` - design decisions and constraints
   - `.apm/plan.md` - Stages, Tasks, agent assignments
   - `.apm/tracker.md` - Task statuses, Worker states, working notes
   - `.apm/memory/index.md` - Memory notes and Stage summaries
   If working notes or Stage summaries reference specific Task Logs for claims or findings that need verification, read those logs directly. Do not read all Task Logs - comprehensive log review is handled by the subagent in step 2.
2. {SUBAGENT_GUIDANCE} Prompt it to read all `.apm/` artifacts including Task Logs in `.apm/memory/` and cross-validate against the current codebase. The subagent verifies that deliverables exist, commits are on the expected branches, and key validation results hold. It reports back: per-Stage outcomes with log-level detail, codebase staleness relative to `.apm/` state, and notable findings from execution.
3. Write `.apm/session-summary.md` per the session summary structure below. The summary is a point-in-time snapshot - state this explicitly.
4. Present the summary to the User. Ask if there is anything to add, correct, or emphasize before finalizing. If the User provides input, update the summary accordingly.
5. Ask: "Would you like me to archive this session?" If yes, ask whether the User wants a custom archive name or the default dated name.
   - If the User declines, no further action.
   - If the User approves, continue to step 6.
6. Run `apm archive --force` or `apm archive --force --name <name>` if a custom name was provided. The CLI snapshots all `.apm/` artifacts (planning documents, Tracker, Memory) into `.apm/archives/<name>/`, writes archival metadata to `metadata.json` inside the archive, then removes the installed files and `.apm/metadata.json` from the workspace root - leaving it clean for a new session while preserving archives.
7. Read `.apm/archives/index.md`. If it does not exist or is malformed, create it per the archive index format below.
8. Append an entry for the newly archived session to the index table.
9. Confirm to the User that archival is complete. To start a new APM session, the User runs `apm init` (or `apm custom`) in the terminal - this is a CLI command, not an assistant command. The CLI downloads and extracts fresh templates (commands, guides, skills, agents) and scaffolds a new `.apm/` directory with blank planning documents (Spec, Plan, Tracker, Memory Index) and a new `metadata.json` tracking the installation. After init completes, the User starts a new Planner with `/apm-1-initiate-planner` in their assistant. Archives from previous sessions remain in `.apm/archives/` and are accessible to the new Planner during Context Gathering.

**Session summary structure:**

*Location:* `.apm/session-summary.md`

*YAML frontmatter schema:*
```yaml
---
date: <YYYY-MM-DDTHH:MM:SSZ>
project: <project name>
stages_completed: <number>
total_tasks: <number>
outcome: <complete | partial | incomplete>
---
```

*Field descriptions:*
- `date`: string, required, ISO 8601 datetime of summary creation (includes date and time).
- `project`: string, required, project name from the Spec.
- `stages_completed`: integer, required, number of completed Stages.
- `total_tasks`: integer, required, total Tasks across all Stages.
- `outcome`: enum, required, derived from session state. `complete` when the Tracker's YAML frontmatter contains `completed_at` (Manager has authoritatively confirmed). `partial` when `stages_completed >= 1` (at least one Stage finished). `incomplete` when `stages_completed == 0` (no Stages finished).

*Body sections* (order as listed):
- *Project Scope:* What was being built and why, from the Spec.
- *Stages and Outcomes.* Per-Stage summary: objective, Tasks completed, notable results.
- *Key Deliverables:* Primary outputs with file paths or descriptions.
- *Codebase State.* Compare the actual codebase against the planning artifacts: which planned deliverables exist as code, which are partial or missing, whether the code evolved past what the Spec and Plan describe, and any gaps between what was planned and what was implemented.
- *Notable Findings:* Operational lessons and patterns - issues encountered and how they were resolved, self-corrections during execution, recovery events, coordination insights. Draw from Memory notes, working notes, and Task Logs.
- *Known Issues:* Unresolved problems, open questions, or caveats.
- *Snapshot Notice:* "This summary reflects the session state as of `<datetime>`. The codebase may have diverged since this summary was created."

**Archive index format:**

*Location:* `.apm/archives/index.md`

```markdown
# APM Archive Index

| Archive | Date | Scope | Stages | Tasks |
|---------|------|-------|--------|-------|
```

*Field descriptions:*
- *Archive:* directory name (e.g., `session-2026-03-04-001`).
- *Date:* ISO date of archival.
- *Scope:* brief project description (from session summary or Spec title).
- *Stages:* number of completed Stages.
- *Tasks:* total Tasks.

Newest entries go at the top of the table.

---

**End of Command**
