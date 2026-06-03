# APM {VERSION} - Task Execution Guide

## 1. 适用角色

**Reading Agent:** Worker

本指南定义 Worker 如何执行 Manager 通过 Task Prompt 分派的任务，包括接收、依赖上下文整合、实现、验证、修正循环、提交、日志和报告。

---

## 2. 执行标准

- 遵循当前代码库已有结构、命名、风格和测试习惯。
- 小步实现，每完成一个有意义的修改就验证一次。
- Task Prompt 和 `{RULES_FILE}` 优先于通用最佳实践。
- 不做无关重构，不扩大任务范围。
- 面向用户的说明使用中文；代码、命令、路径、字段保持原样。

### 2.1 上下文整合

如果 Task Prompt 中 `has_dependencies: true`，先读取依赖上下文。

- **Same-agent dependency**：这是你自己之前完成的工作。使用轻量回忆上下文，必要时读取提示中的路径。
- **Cross-agent dependency**：这是其他 Worker 完成的工作。必须完整读取依赖说明中的文件、接口和产物摘要，不要靠猜测集成。

如果依赖基础不稳定：

- 跨 Agent 依赖不清楚时，暂停并请求用户或 Manager 指导；
- 同 Agent 的轻微歧义可以按最合理解释继续，但必须在 Task Log 中说明。

### 2.2 验证标准

按 Task Prompt 的 Validation Criteria 执行所有可自主验证项：

- 运行测试；
- 运行构建或 lint；
- 验证文件和产物存在；
- 验证行为符合要求。

如果自主验证失败，先自行修复，不要要求用户审查失败状态。

如果验证需要用户判断或外部环境，例如设计确认、线上平台操作、账号权限、外部 API 行为，必须在自主验证全部通过后暂停，并清楚说明用户需要检查什么、怎么反馈。

### 2.3 修正循环

验证失败时：

1. 先读错误输出，定位根因。
2. 每轮只做一个有针对性的修复。
3. 修复后重新验证。
4. 如果同类问题反复出现、根因不清、或多个独立区域都可能导致问题，使用 debug subagent。{WORKER_SUBAGENT_GUIDANCE}
5. subagent 返回后，必须验证它的结论，再应用修复。
6. 如果仍无法解决，使用 `Partial` 状态报告，说明已尝试内容、失败原因和需要的指导。

### 2.4 规则更新

如果用户在执行中给出纠正或新要求，先遵守并继续任务。任务完成后：

- 在 Task Log 的 Important Findings 中记录；
- 将 `important_findings` 设为 `true`；
- 报告后询问用户是否要把该纠正提升为 `{RULES_FILE}` 中的通用 Rule。

### 2.5 版本控制

Worker 只在 Task Prompt 指定的工作区和分支上工作。

- 可以 commit；
- 不创建分支；
- 不管理 worktree；
- 不 push；
- 不 merge。

Commit message 描述真实代码变化，不出现 APM、Task ID、Stage、Worker 等框架词。

### 2.6 Batch 规则

如果收到 batch：

- 按顺序执行；
- 每个 Task 独立执行、验证、写 Task Log；
- 某个 Task `Failed` 时立即停止 batch；
- 最后写一份 batch report。

---

## 3. 执行流程

### 3.1 接收 Task Prompt

1. 判断是否为 batch。
2. 校验 YAML frontmatter 中的 `agent` 是否匹配当前 Worker。
3. 如果有 Workspace section，切换到指定分支或 worktree。
4. 如果 `has_dependencies: true`，先执行上下文整合。

### 3.2 实现

1. 按 Detailed Instructions 顺序执行。
2. 应用 Guidance 和 `{RULES_FILE}` 中相关规则。
3. 需要用户动作时，说明原因、选项和期望反馈。
4. 需要 subagent 时，给出结构化任务：目标、上下文、文件路径、预期输出。
5. 完成实现后，告知用户你将进入验证。

### 3.3 验证

1. 执行所有自主验证项。
2. 失败则进入修正循环。
3. 需要用户参与的验证，在自主验证通过后暂停。
4. 全部通过则进入完成流程。

### 3.4 完成

1. 在聊天中用中文说明完成评估：
   - 目标是否达成；
   - 验证是否通过；
   - 是否有 important findings；
   - 是否有 compatibility issues；
   - outcome 是 `Success`、`Partial` 还是 `Failed`。
2. 按规则 commit。
3. 按 `{GUIDE_PATH:task-logging}` 写 Task Log。
4. 按 `{GUIDE_PATH:task-logging}` 写 Task Report。
5. 告知用户回到 Manager 会话运行：

   ```text
   /apm-5-check-reports <agent-id>
   ```

   Codex CLI 使用：

   ```text
   $apm-5-check-reports <agent-id>
   ```

---

## 4. 常见错误

- 没读依赖文件就开始实现；
- 验证失败时盲目改代码；
- Task 未完全验证就标记 `Success`；
- 在 commit message、代码注释或用户可见产物中写 APM 内部术语；
- 把 Manager 的协调职责拿到 Worker 会话里做；
- 忽略 Task Prompt 的范围限制。

---

**Guide 结束**
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
# APM {VERSION} - Task Execution Guide

## 1. Overview

**Reading Agent:** Worker

This guide defines how you execute Tasks assigned by the Manager via Task Prompts, from receipt through context integration, execution, validation, iteration, and completion.

---

## 2. Operational Standards

Write clean, maintainable code following best practices for the language and framework in use. Use descriptive naming and add comments where the logic is not self-evident. Follow the existing codebase's patterns, conventions, and structure. Build incrementally - validate after each meaningful step rather than producing everything at once. These are baseline defaults; Task Prompt instructions and Rules take precedence when they specify otherwise.

### 2.1 Context Integration Standards

Follow cross-agent integration steps completely - read files, review artifacts, understand interfaces. For dependency integration that requires reading specific files at known paths, read them directly. Subagent dispatch is for open-ended exploration or investigation where the scope is broad or context isolation is beneficial. Use same-agent guidance as recall anchors - review referenced paths to refresh context if needed.

**Integration issues:** Do not execute on an unstable foundation. For cross-agent dependencies, pause for User guidance. For same-agent, minor ambiguities - continue with best interpretation and note uncertainty; missing expected files - pause for guidance.

### 2.2 Validation Standards

Validation criteria in the Task Prompt specify what to check. Execute each criterion as written - run tests, verify outputs exist and match expected structure, confirm behavior meets requirements. Always complete autonomous checks first. If any autonomous check fails, correct it before involving the User - do not request User review or User action while autonomous checks are failing.

When a criterion requires User involvement - judgment the Worker cannot self-assess (design approval, content quality) or action outside the development environment (running external checks, confirming platform behavior) - pause and present work only after all autonomous checks pass. When pausing, communicate clearly per `{SKILL_PATH:apm-communication}` §2.1 Direct Communication: what is needed and why, what the User should expect or verify, and what to report back so execution can continue.

When criteria require resources not currently available, request them from the User rather than substituting a lower verification level.

### 2.3 Iteration Standards

When validation fails, you enter a correction loop - investigate, correct, re-validate.

**Investigate before fixing.** Read error output thoroughly, trace the failure to its origin, and understand what specifically went wrong before changing anything. Attempting fixes without understanding the cause compounds problems and wastes iterations.

**One targeted fix per iteration.** Apply a single change based on what your investigation found, then re-validate. When a correction does not resolve the issue - the same failure recurs, the fix introduces new problems, or the root cause remains unclear - spawn a debug subagent with structured instructions: the error output, what you investigated and attempted, relevant file paths, and the expected vs actual behavior. Direct it to trace the root cause, form a specific hypothesis, and propose a targeted fix. The subagent iterates in a fresh context while your main context is preserved for validating its findings. When the root cause could stem from multiple independent areas, spawn separate subagents in parallel. When a subagent returns, validate its findings before applying - confirm the root cause explanation makes sense and the fix addresses it. If unresolved after subagent investigation, prefer reporting back with Partial status - the Manager can restructure or reassign. When execution suggests Task Prompt instructions may be inaccurate, this is also a reason to stop iterating. When classification is unclear, prefer Partial with clear description - invites guidance rather than closing options.

**User collaboration:** Pause when criteria require User judgment (you cannot self-approve subjective quality), when explicit User actions are needed (outside the development environment), when environment resources are needed for validation, or when iteration stalls and you need guidance. Continue autonomously when checks can be performed without User involvement and when the cause of failure is clear and the fix is within scope. When uncertain or stopping without Success, pause and present the situation to the User with options rather than making unilateral decisions.

### 2.4 Rules Updates

When the User provides a correction or directive during execution, comply immediately and continue. Do not pause to discuss Rules at this point. At Task completion, note the correction in the Task Log under Important Findings with `important_findings: true` - the Manager will see it during Task Review regardless of what happens next. After logging, reporting, and directing the User to deliver the report, ask at the end of your turn whether the correction should become a Rule for all Workers - frame it naturally based on what was said and why it might apply beyond this Task. Make it clear the User can ignore this and proceed with delivering the report - it is not a gate. If the User approves, update `{RULES_FILE}` and update the Task Log to note that the correction was entered as a Rule. If the User declines, defers, or ignores, no further action - the Manager already has visibility through the important findings flag.

### 2.5 Version Control Standards

Operate in the workspace provided by the Task Prompt - main working directory on the assigned branch for sequential dispatch, or worktree path for parallel dispatch. Commit work to the assigned branch following the commit conventions from `{RULES_FILE}` and note the workspace in the Task Log. You only commit - do not create branches, manage worktrees, push, or merge. The Manager handles all other version control operations. For large Tasks, commit at logical intermediate points during execution rather than only at completion - each commit should represent a coherent unit of change.

**Commit content:** APM terminology - Task IDs, Stage numbers, agent identifiers, framework vocabulary - does not appear in commit messages, branch references, or source code comments. Commits reflect the actual code changes and actions taken, not the framework managing them. Write commit messages as if no project management framework existed.

### 2.6 Batch Rules

When receiving a batch of Tasks (multiple Task Prompts in a single Task Bus message), execute sequentially. Complete each Task fully - execute, validate, and write the Task Log - before starting the next Task in the batch. Each Task gets its own Task Log at its specified `log_path`.

**Fail-fast:** If any Task results in Failed status, stop the batch. Do not proceed to remaining Tasks. After completing all Tasks (or stopping on failure), write a single batch report to the Report Bus per `{GUIDE_PATH:task-logging}` §4.3 Batch Report Format. Do not defer logging to the end of the batch.

---

## 3. Task Execution Procedure

Sequential flow from Task Prompt receipt through completion. Task Validation and the Correction Loop form a cycle that repeats until success or a stop condition.

### 3.1 Task Prompt Receipt

On Task receipt, perform the following actions:
1. Check for batch envelope: if Task Bus contains `batch: true` in frontmatter, it contains multiple Task Prompts separated by `---` delimiters. Execute each Task sequentially per §2.6 Batch Rules.
2. Verify `agent` in YAML frontmatter matches your assigned identity. Validate the bus directory matches `agent` per `{SKILL_PATH:apm-communication}` §4.1 Bus Identity Standards. If mismatch, decline per `{COMMAND_PATH:apm-3-initiate-worker}` §5 Operating Rules.
3. If Workspace section present: switch to the specified branch or worktree path before starting work.
4. If `has_dependencies: true`, continue to Context Integration, otherwise proceed to §3.3 Task Execution.

### 3.2 Context Integration

Perform the following actions:
1. Read the Context from Dependencies section.
2. Execute integration based on dependency type per §2.1 Context Integration Standards:
   - **Cross-agent:** Follow integration steps completely - read files, review artifacts, understand interfaces. {WORKER_SUBAGENT_GUIDANCE} When a subagent returns findings, verify critical claims by reading the key files it references before proceeding - subagent summaries compress details and can misrepresent what matters for execution.
   - **Same-agent:** Use guidance to recall and build upon prior work; review referenced paths to refresh context if needed.
3. If integration issues discovered, apply decision rules from §2.1 Context Integration Standards.

### 3.3 Task Execution

Perform the following actions:
1. Execute Detailed Instructions sequentially, applying Guidance and relevant Rules from `{RULES_FILE}`, working toward the Objective.
2. When an instruction requires explicit User action, communicate what needs doing, why, and what options exist. Await completion, then resume.
3. When an instruction includes a subagent step, spawn the relevant subagent with a structured task description. Verify critical findings by reading key files the subagent references before integrating into execution. {WORKER_SUBAGENT_GUIDANCE}
4. When all instructions complete, communicate that implementation is complete and you are moving to validation. Continue to Task Validation.

### 3.4 Task Validation

Perform the following actions:
1. Execute autonomous checks from the Task Prompt's validation criteria per §2.2 Validation Standards: run tests, verify builds, confirm outputs exist and match expected structure. If any fail, continue to the correction loop. Ambiguous results: treat as failure and iterate; if iteration doesn't resolve, pause for guidance.
2. If criteria require User involvement: pause and present work per §2.2 Validation Standards. Communicate what was accomplished, what needs the User's review or action, where deliverables are located, and what to report back. If approved or completed, proceed to §3.6 Task Completion with Success status. If feedback provided, continue to the correction loop with feedback integrated.
3. If all criteria passed, proceed to §3.6 Task Completion with Success status.

### 3.5 Correction Loop

Perform the following actions:
1. Investigate the failure per §2.3 Iteration Standards: read error output, trace the cause, understand what went wrong.
2. Apply a single targeted fix based on your investigation, re-execute affected portions, and return to Task Validation.
3. If the correction does not resolve the issue, spawn a debug subagent per §2.3 Iteration Standards: provide the error output, what you investigated and attempted, relevant file paths, and expected vs actual behavior. Direct it to trace the root cause and propose a fix.
4. When the subagent returns, validate its findings - confirm the root cause and verify the fix. If sound, apply and return to Task Validation. If unresolved, present the situation to the User: what failed, what was investigated and attempted, current state, and options for proceeding. Upon User guidance, integrate the new direction or apply outcome status per `{GUIDE_PATH:task-logging}` §2.2 Outcome Standards and continue to Task Completion.

### 3.6 Task Completion

Perform the following actions:
1. Present your assessment visibly in chat: whether all objectives are met and deliverables are ready, whether any important findings or compatibility issues arose, and the Task's outcome status per `{GUIDE_PATH:task-logging}` §2.2 Outcome Standards.
2. Commit work to the assigned branch per §2.5 Version Control Standards.
3. Create Task Log per `{GUIDE_PATH:task-logging}` §3.1 Task Log Procedure at `log_path`.
4. Write Task Report per `{GUIDE_PATH:task-logging}` §3.2 Task Report Delivery. Include relevant status indications:
   - *After Handoff.* If this is the first Task after Handoff initialization, include incoming Worker indication: state instance number, list the specific Task Log files loaded, and note that previous-Stage logs were not loaded.
   - *After recovery:* If auto-compaction occurred and recovery was performed via `/apm-9-recover`, note it in the Task Report so the Manager is aware.
5. State readiness for the next Task via `/apm-4-check-tasks` (no argument needed - you are already registered). Await the next Task Prompt or Handoff initiation.

---

## 4. Common Mistakes

- *Framework vocabulary in project output:* Commit messages, source comments, and code should describe the actual work - not the framework managing it. Never surface Task IDs, Step numbers, agent identifiers, or APM terminology in project-facing output.
- *Skipping cross-agent integration steps:* When cross-agent dependency context includes file reading instructions and integration guidance, completing those steps fully before starting implementation catches integration mismatches early. Proceeding on assumptions about another Worker's output leads to rework.
- *Fixing without investigating:* Attempting changes before understanding why the failure occurred. Read error output, trace the cause, and understand what went wrong first - otherwise each fix attempt is a guess that may compound the problem.
- *Continuing to iterate instead of delegating:* When a correction does not resolve the issue, the effective path is spawning a debug subagent with accumulated context rather than continuing in the main context. Each iteration consumes context budget and reduces reasoning quality - a subagent with fresh context is more effective.
- *Working non-incrementally:* Writing large deliverables in one pass without testing intermediate results. Build incrementally - compile, run, or validate after each meaningful step rather than producing everything and then discovering issues.
- *Logging Success with incomplete validation:* Marking a Task as Success when validation criteria were not fully exercised. If criteria cannot be met (missing resources, need User cooperation), log as Partial and explain what remains rather than claiming Success with caveats.

---

**End of Guide**
