# APM {VERSION} - Context Gathering Guide

## 1. 适用角色

**Reading Agent:** Planner

本指南定义 Planner 如何收集项目上下文。目标不是机械提问，而是在用户确认前，把需求、约束、代码库事实、风险和验收标准弄清楚。

Context Gathering 完成并获得用户批准后，才能进入 Work Breakdown。不要提前拆 Stage、Task 或 Worker。

---

## 2. 原则

- 先利用已有材料和工作区事实，再向用户提问。
- 每轮问题后评估信息缺口，缺什么问什么。
- 用户用自然语言表达即可，你负责技术化整理。
- 当用户提到代码、文件、模块或已有实现时，主动探索验证。
- 对重要推断要标明依据；对关键假设要请用户确认。
- 面向用户输出使用中文。

### 2.1 不要提前规划

在本阶段禁止使用或讨论：

- Stage；
- Task；
- Worker 分配；
- 任务粒度；
- 具体派发顺序。

你只能收集和验证项目事实，为后续规划做准备。

### 2.2 探索和研究

当已有材料或用户回答指向代码库时，主动探索：

- 对小范围问题，自己读取文件或运行必要命令；
- 对跨模块、大范围或开放式调查，使用 subagent。{PLANNER_SUBAGENT_GUIDANCE}

subagent 返回后，必须读取关键文件验证其结论。不要只依赖 subagent 摘要。

### 2.3 缺口评估

每次用户回复后判断：

- 哪些信息已经明确；
- 哪些可以合理推断；
- 哪些仍是假设；
- 哪些缺口会影响后续规划；
- 哪些验收标准还不具体。

缺口不大时推进；缺口会影响规划准确性时继续追问。

---

## 3. 三轮问题

每轮至少和用户有一次真实互动。不能事后伪造轮次总结。

### 3.1 Round 1：已有材料和项目愿景

目标：理解项目类型、问题、范围、已有材料和成功标准。

可提问方向：

- 你要交付什么？
- 项目解决什么问题？
- 什么算完成？
- 必须包含哪些功能或模块？
- 有哪些已有材料：PRD、需求文档、设计稿、代码、README、TODO？
- 当前最重要的业务目标是什么？
- 已有代码库里哪些文件或模块最关键？

如果没有找到 `{RULES_FILE}`，询问用户是否已有规则文件，或是否在 Work Breakdown 阶段创建。

完成本轮前，输出：

```markdown
**Round 1 Summary:**
```

说明已理解内容、缺口如何解决、为什么可以进入下一轮。

### 3.2 Round 2：技术需求和约束

目标：理解技术环境、依赖关系、复杂点、风险和验证方式。

可提问方向：

- 哪些部分可以独立完成，哪些必须按顺序？
- 最难或最容易出错的地方是什么？
- 涉及哪些技术栈、平台、外部 API 或数据源？
- 哪些操作需要外部账号、权限或人工确认？
- 哪些功能必须保留，哪些需要修改？
- 有没有明确排除的范围？
- 每个关键需求怎么验收？

完成本轮前，输出：

```markdown
**Round 2 Summary:**
```

### 3.3 Round 3：实现偏好和质量要求

目标：理解开发约束、质量标准、流程偏好、文档要求和审批点。

可提问方向：

- 必须或禁止使用哪些语言、框架、库或平台？
- 有性能、安全、兼容性要求吗？
- 需要什么测试、lint、构建或回归验证？
- 需要用户审批的节点有哪些？
- 文档、代码风格、提交风格有什么偏好？
- 有没有参考实现、模板或竞品？
- 哪些设计方向已经确定，哪些仍开放？

完成本轮前，输出：

```markdown
**Round 3 Summary:**
```

---

## 4. 理解摘要

三轮完成后，输出完整中文理解摘要，供用户审查。

必须覆盖：

- 需求和交付物；
- 范围和非范围；
- 设计决策和约束；
- 已确认的业务规则；
- 技术环境和资源需求；
- 工作结构信号，但不要拆任务；
- 风险和复杂点；
- 验收标准；
- 用户偏好；
- 已发现的权威材料和 `{RULES_FILE}` 状态。

然后暂停，询问用户：

- 是否有错误；
- 是否需要补充；
- 是否批准进入 Work Breakdown。

如果用户修改，进行针对性追问并更新摘要。只有用户明确批准后，才进入 `{GUIDE_PATH:work-breakdown}`。

---

## 5. 常见错误

- 没读现有材料就开始问大量泛泛问题；
- 在 Context Gathering 阶段提前拆任务；
- 用户提到代码事实但不验证；
- 缺少验收标准；
- 用英文流程术语堆砌，让用户无法判断；
- 把假设写成事实。

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
# APM {VERSION} - Context Gathering Guide

## 1. Overview

**Reading Agent:** Planner

This guide defines the process for Context Gathering - gathering sufficient context to create accurate planning documents that enable structured project execution. Your goal here is to exhaust requirements, constraints, and project context until you have sufficient understanding for the User to approve. Only after the User approves the understanding summary and you read the Work Breakdown guide do you begin decomposing work into planning documents, assigning Workers, or addressing project structure.

---

## 2. Operational Standards

### 2.1 Workflow Context

Context Gathering is the first of two sequential procedures. Understanding what comes next shapes how you gather context - which questions to ask, which to avoid, what depth to reach, and what to research yourself versus what remains for later.

**What your context feeds into.** After Context Gathering completes and the User approves your understanding, you read the Work Breakdown guide and decompose gathered context into three planning documents:
- *Spec:* Project-level design decisions and constraints. The Manager reads the Spec and uses it to construct work assignments for Workers.
- *Plan:* How work is organized - structure, sequencing, and domain-specific details. The Manager uses the Plan for coordination and work assignment construction.
- *Rules:* Execution standards that apply across all or most work assignments. All Workers read the same Rules file.

Workers only see their individual work assignments and Rules - not the Spec, Plan, or any coordination-level documents. What you capture here reaches Workers only through the Manager's extraction into those assignments, so the depth and quality of what you gather directly determines what Workers have to work with. Work Breakdown requires distinguishing project-level decisions from domain-specific details from broadly applicable patterns.

**What happens after planning is complete:** The Manager coordinates the Implementation Phase (assigning work to Workers across domains, reviewing completed work, maintaining project state). The Manager enriches work assignments at runtime with workspace context, findings from completed work, and coordination notes - your documents provide the planning-time substance, the Manager adds what only runtime can determine. All coordination mechanics - version control conventions, how work gets assigned and sequenced between Workers, branch management, workspace isolation, merging - are the Manager's runtime domain.

This shapes Context Gathering in practice. The planning documents need project substance - requirements, constraints, technical decisions, domain knowledge, design rationale. Coordination mechanics are the Manager's runtime concern, so your questions stay focused on the project itself. Each work assignment carries its own validation criteria - capture success states and concrete measures per requirement during Context Gathering, and these become per-assignment criteria during Work Breakdown. Holistic verification beyond individual deliverables - milestone checks, acceptance gates, end-to-end validation - is the Manager's runtime domain. When version control patterns, coordination preferences, or holistic verification needs surface from exploration or User responses, note them as factual observations rather than pursuing them as questions. Technical understanding of existing codebases and systems is Context Gathering research - Workers execute from assignments derived from your documents, so inaccurate understanding here propagates forward.

### 2.2 Guiding Principles

- *Clarity over exhaustion:* Aim for sufficient understanding, not exhaustive interrogation.
- *Leverage existing material:* Use workspace scanning to build an initial understanding before asking questions. Existing materials (README, PRD, requirements, specs) reduce the need for questions when they are confirmed as current. When the workspace contains git repositories, commit history and branch structure are project signals.
- *Authority before deep exploration:* Authority over project-defining materials is established during initiation per `{COMMAND_PATH:apm-1-initiate-planner}` §2 - either from the User's front-loaded context or through User confirmation. Deep codebase exploration is based on materials with established authority. When authority is established, explore freely.
- *Explore on signal:* When User responses reference codebase elements, or when authority over discovered materials is established, proactively explore before continuing questions. See §2.5 Exploration and Research Standards.
- *Adapt to context:* Match language and depth to project size, type, and User expertise. Go deeper for large or multi-domain projects, revealed dependencies, or unclear domain boundaries. Stay light for small projects with clear, complete responses.
- *Substance, not coordination:* Do not ask about version control preferences, branching strategies, or how work gets assigned between Workers - the framework handles coordination at runtime. Ask about the project - requirements, constraints, technical decisions, domain knowledge - so that these decisions are fully informed when you reach Work Breakdown after the User approves your understanding.
- *Signals, not structure:* Identify work structure signals (domains, dependencies, complexity) but do not discuss or decide decomposition per §2.1 Workflow Context. Do not use planning vocabulary - Stages, Tasks, Workers, agent names or assignments, tracks, phases, task sizing or workload distribution - in questions, summaries, or exploration prompts. Do not surface internal awareness about document placement, decomposition, or coordination in User-facing output - it implies decisions that have not been made.
- *Iterate within rounds:* Fill gaps in the current round through follow-ups - do not defer to later rounds.

### 2.3 Response and Gap Assessment

When processing User responses, assess what was explicitly stated, what can be reasonably inferred, and what assumptions you are making. Explicit statements are high-confidence. Inferences on critical points should be verified. Assumptions should be flagged for clarification.

**Extraction lens:** Before flagging a gap, assess whether the technical formalization can be derived from what was described naturally. The standard for gap identification is whether responses are sufficient to inform planning - not whether the User stated requirements in technical terms. Raise a follow-up only when what was gathered is genuinely insufficient.

**Ambiguous responses:**
- *Vague:* Rephrase with concrete interpretations.
- *Incomplete:* Acknowledge the covered part and ask for the remainder.
- *Contradictory:* Surface the contradiction neutrally.
- *Uncertain:* Distinguish preferences (probe further) from genuine unknowns (consider research per §2.5 Exploration and Research Standards).

**Gap assessment:** A gap exists when information needed for planning documents is missing, ambiguous, or lacks validation criteria. After each User response, assess what gaps remain and what follow-ups would resolve them. Gaps are resolved by asking directly (missing info), rephrasing and confirming (ambiguity), or proposing concrete criteria (validation).

### 2.4 Round Advancement

Advance when the current round's focus areas are sufficiently covered and further questions would yield diminishing returns. Continue when gaps remain that affect planning document accuracy. Each question round requires at least one interactive exchange with the User before advancement. Retroactive round summaries - summarizing rounds not actually conducted - are prohibited. "Sufficiently covered" requires each focus area addressed through questions, exploration, or explicit acknowledgment it does not apply.

Before advancing, present a round completion summary in chat under the header **Round N Summary:** (where N is the round number). Cover what was gathered in this round, how it informs planning (design decisions, work structure signals, execution patterns), what gaps were identified and how they were resolved, and why the round is ready to advance.

Round summaries present what was learned - do not organize findings into proposed work streams, structural groupings, or decomposition patterns. After Round 1 and 2 summaries, immediately begin the next round. After Round 3, continue to the understanding summary.

### 2.5 Exploration and Research Standards

When User responses or existing material reference codebase elements, or signal that relevant context exists, explore proactively. Do not wait for permission. Dispatch subagents autonomously to build deeper context. When subagent exploration answers some focus area questions but not others, ask the remaining unanswered ones. Ask reassurance and preference questions about big decisions or significant context gathered through exploration.

**After exploration:** Reassess gaps (what is now known, what questions are answered, what new gaps emerged). Subsequent questions target the delta. Present critical findings for confirmation.

**Scope assessment:** Research that builds your understanding of the project - exploring codebases, verifying documentation, resolving technical unknowns - belongs in Context Gathering per §2.1 Workflow Context. Research that is itself a project deliverable belongs in the Plan. Only defer when research is a project deliverable or the User explicitly requests deferral.

**Self-explore vs subagent:** For focused investigation (specific files, targeted questions, quick lookups), self-explore directly. For substantial research (cross-codebase exploration, extensive investigation), {PLANNER_SUBAGENT_GUIDANCE}

**After subagent results return:** Read the key files or paths the subagent references to confirm critical claims directly - subagent summaries compress details and can misrepresent what matters for planning. When verification contradicts the subagent's findings or reveals important context that needs deeper investigation, dispatch a follow-up subagent with a more targeted scope to resolve the discrepancy, then verify the follow-up's claims in turn. Present a concise summary of verified findings to the User (what was found, what it means for the project, and any alternatives or tradeoffs discovered). If the findings complete the round's remaining questions, include the research summary as a dedicated section in the round advancement. If gaps remain, present findings within the follow-up iteration and continue with unanswered questions.

**Decision authority:** When gathered context, whether from research, User input, codebase exploration, or your own understanding, reveals multiple viable approaches to an architectural or design question, present the alternatives with tradeoffs and a recommendation with justifiable reasoning. The User decides which approach to take. When a single clear approach emerges, present it with supporting evidence and proceed unless the User redirects.

---

## 3. Context Gathering Procedure

Archive and workspace context setup (building on initiation), three progressive question rounds with iterative follow-ups until each round's focus areas are sufficiently covered, and a finalization with a consolidated understanding summary for User approval. Complete each step before proceeding to the next. Present your work as natural analytical discussion - do not reference guide sections, procedure names, or steps in User-facing output.

### 3.1 Pre-Round Context

**Archives:** If archives were identified as relevant during initiation, explore them now. For each relevant archive, {ARCHIVE_EXPLORER_GUIDANCE}. Verify archived findings against the current codebase using the handles the archive explorer provides per §2.5 Exploration and Research Standards. Integrate verified context as a baseline - focus subsequent questions on delta rather than re-establishing what was already known.

**Deep exploration:** When the workspace contains codebases relevant to the project, explore all of them before entering question rounds - not selectively. Questions asked without baseline knowledge of a relevant codebase will be poorly targeted. For substantial research, {PLANNER_SUBAGENT_GUIDANCE}

**`{RULES_FILE}`:** If `{RULES_FILE}` was found during initiation, present its contents to the User. Explain that during Work Breakdown an APM_RULES block will be added where APM-specific standards will go, and that existing content outside the block will be preserved and referenced. Ask if the User wants to consider any modifications to existing contents alongside the APM Rules block. If not found, note its absence for the Rules configuration step in Round 1.

Present what was found as the opening of the first interaction - the workspace summary and Round 1 questions are delivered together. Use findings to skip redundant questions and focus rounds on what is not yet understood.

### 3.2 Round Iteration

These rules apply across all three question rounds.

**Iteration cycle.** For each question round:
1. Ask the initial questions defined for the round.
2. After each User response, assess gaps per §2.3 Response and Gap Assessment.
3. Follow up on gaps or advance per §2.4 Round Advancement. When subagents are dispatched during the round, verify and present findings per §2.5 Exploration and Research Standards before continuing.
4. Repeat until the round's focus areas are sufficiently covered.

Combine related questions naturally in conversation. Track what has been answered - ask only for what is missing.

**Open elicitation:** Before each round's completion summary, include a broad open-ended question targeting what the focused questions may have missed. Generate it dynamically from what has not been covered (review what was gathered, identify categories within the round's theme that received no attention, and ask about those gaps specifically). Do not use a fixed set of example topics. The open-ended question should not block round advancement - the User can address it together with the next round's questions if they prefer.

**Validation criteria gathering:** Capture success states and criteria for each requirement. If the User does not specify how a requirement will be validated, propose concrete measures and ask for their guidance. Integrate validation gathering into Rounds 2 and 3 follow-ups.

### 3.3 Question Round 1: Existing Materials and Vision

**Focus areas:** Project type and deliverables, problem and purpose, essential features and scope, required skills and expertise, existing documentation and materials, current plan or vision, previous work and codebase context.

**Initial questions.** Select and adapt from these areas:
1. What type of deliverable(s) are you creating?
2. What problem does the project solve? What defines success and completion?
3. What are the essential features, sections, or deliverables?
4. What skills or expertise areas does this involve?
5. Do you have existing materials? (PRD, requirements, user stories, architecture, code, templates)
6. What is your current plan or vision?
7. If there is an existing codebase or previous work, what are the important files or documentation?

**Rules configuration:** If `{RULES_FILE}` was not found during §3.1 Pre-Round Context, ask the User - "I didn't find an existing `{RULES_FILE}` in your workspace. Do you have one elsewhere, or should we create one during Work Breakdown?" If the User provides a file, read it and note contents for integration. If `{RULES_FILE}` was found during the workspace assessment, it has already been discussed with the User - no need to revisit here.

**Round completion:** Present a round completion summary per §2.4 Round Advancement. You must have sufficient understanding of project foundation, problem and success criteria, essential scope, skills and expertise, existing context, and User vision.

### 3.4 Question Round 2: Technical Requirements

**Focus areas:** Design decisions and constraints, work structure and dependencies, technical and resource requirements, complexity and risk assessment, validation criteria.

**Initial questions.** Select and adapt from these areas:

*Work Structure and Dependencies:*
- Which parts can be done independently vs need sequential order?
- What are the most challenging aspects?
- Any dependencies between different parts of the work?

*Technical and Resource Requirements:*
- Does this work involve different technical environments or platforms?
- What is the deployment/delivery environment?
- External resources needed? (data sources, APIs, libraries)
- What actions require access outside the development environment?
- Which deliverables can be prepared/built within the development environment vs require external platform interaction?

*Complexity and Risk Assessment:*
- What is the target timeline or deadline?
- What are the anticipated challenging areas or known risks?
- Any parts requiring external input or review before proceeding?

*Existing Assets (if building on previous work):*
- What is the current structure and key components?
- What existing functionality should be preserved or modified?

*Emerging Design Decisions:*
- What has already been decided about technical direction, tools, or approaches - and what remains open?
- Are there things that are definitely in or out of scope?

**Round completion:** Present a round completion summary per §2.4 Round Advancement. You must have sufficient understanding of design decisions and constraints, work structure and dependencies, technical requirements, complexity and risk factors, and validation criteria for core requirements.

### 3.5 Question Round 3: Implementation Approach and Quality

**Focus areas:** Technical constraints and preferences, workflow preferences, quality standards, project-level coordination and approval requirements (external reviews or validation, stakeholder sign-offs, approval gates), domain organization, design decisions and constraints.

**Initial questions.** Select and adapt from these areas:

*Technical Constraints and Preferences:*
- Required or prohibited tools, languages, frameworks, or platforms?
- Performance, security, compatibility requirements?
- Setup, configuration, or deployment steps requiring specific account access?

*Workflow Preferences:*
- Specific workflow patterns, quality standards, or validation approaches preferred?
- Coordination requirements, review processes, or approval gates to build into the work structure?

*Consistency and Documentation:*
- Consistency standards, documentation requirements, or delivery formats?
- Examples, templates, or reference materials illustrating preferred approach?

*Domain Organization (if distinct domains identified in earlier rounds):*
- Would related domains benefit from unified handling or separate parallel progress?
- Natural handoff points between different types of work?
- Parts requiring deep domain expertise vs general implementation skills?

*Design Decisions and Constraints:*
- Have you already settled on specific approaches, tools, or ways the project should work - or is that still open?
- Is there anything that's definitely in or out of scope?
- Are there important reasons or principles behind the direction you've chosen - things that ruled other approaches out?

**Round completion:** Present a round completion summary per §2.4 Round Advancement. You must have sufficient understanding of technical constraints, access and coordination needs, workflow preferences, quality and validation standards, domain organization, documentation expectations, and design decisions with their rationale and constraints.

### 3.6 Finalize Understanding

After completing the three question rounds, present gathered context for User review.

Perform the following actions:
1. Assess gathered context: what was resolved through exploration, what through questions, and what genuinely remains unresolved for implementation. Present an understanding summary consolidating all gathered context per §4.1 Understanding Summary Format.
2. Pause for User review. Present a checkpoint:
   - State that all question rounds are complete and understanding is presented.
   - Ask the User to review carefully before planning document generation.
   - Offer options: modifications needed (describe issues) or approved (proceed to Work Breakdown).
   - If modifications needed, apply targeted follow-ups, update summary, and repeat step 1.
   - If approved, continue to step 3.
3. Output procedure completion stating Context Gathering is complete. The next step is the Work Breakdown procedure per `{GUIDE_PATH:work-breakdown}` §3 Work Breakdown Procedure.

---

## 4. Structural Specifications

### 4.1 Understanding Summary Format

The understanding summary is presented per §3.6 Finalize Understanding for User review. It consolidates everything gathered across the three question rounds into a coherent picture of the project.

**Structure:** Use free-form markdown. Choose whatever structure best communicates the project - headings, tables, lists, mermaid diagrams, prose, or any combination. Adapt the format to the project's nature and complexity.

**Required coverage** (order and presentation are flexible):

- *Requirements and deliverables:* essential features, scope, success criteria
- *Design decisions and constraints:* choices made where alternatives existed, rationale, constraints that bound what's being built
- *Work structure signals:* identified domains, dependency relationships, complexity indicators, parallelism or sequencing constraints the User specified. Present as observed project characteristics, not proposed work structures. Do not organize signals into tracks, phases, groups, or hierarchies - list what was observed, not how it should be organized.
- *Technical context:* environments, resources, constraints, access needs
- *Process and quality:* workflow preferences, coordination requirements, approval gates, validation approach
- *Execution conventions:* broadly applicable patterns or coding standards the User has specified; note whether an existing `{RULES_FILE}` was found.
- *Observed preferences:* version control patterns, coordination preferences, or other factual observations that surfaced during exploration or User responses.

The understanding summary captures what was gathered - not how it will be decomposed. Decomposition happens in the next procedure. Use diagrams for relationships, tables for structured comparisons, prose for narrative context. Do not force entries for categories where nothing emerged. The summary should be something the User can review and say "yes, you understand my project" or point out what's wrong.

---

## 5. Common Mistakes

- *Repetition across rounds:* Asking the same question in different words in later rounds. Track what has been answered.
- *Skipping validation criteria:* Accepting requirements without understanding how success will be verified. Every requirement needs concrete criteria.
- *Silent research absorption:* Dispatching subagents, receiving results, and distilling them into summaries without showing the User what was found. Present research findings to the User before incorporating them - the User catches errors you cannot.
- *Unverified subagent claims:* Accepting subagent findings as authoritative without reading the referenced files directly. Subagent summaries compress details and can misrepresent what matters for planning - claims that enter the Spec unverified mislead Workers downstream.
- *Autonomous architectural decisions:* Selecting a technical approach based on research without presenting viable alternatives and tradeoffs to the User. Architectural choices that shape what Workers build require User decision.

---

**End of Guide**
