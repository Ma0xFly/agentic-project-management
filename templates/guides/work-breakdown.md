# APM {VERSION} - Work Breakdown Guide

## 1. 适用角色

**Reading Agent:** Planner

本指南定义 Planner 如何把已批准的理解摘要转换为 3 份规划产物：

- `Spec`：定义要构建什么；
- `Plan`：定义工作如何组织；
- `{RULES_FILE}`：定义所有 Agent 如何执行工作。

每份文档都必须先展示分析，再写入文件，然后等待用户批准。

---

## 2. 文档职责

### 2.1 Spec

`Spec` 记录项目级事实：

- 需求和范围；
- 设计决策；
- 技术约束；
- 业务规则；
- 工作区结构；
- 权威材料；
- 验收标准。

Manager 会读取 `Spec`，再把相关内容提取进 Task Prompt。Worker 不直接读取 `Spec`。

### 2.2 Plan

`Plan` 记录工作组织方式：

- Workers；
- Stages；
- Tasks；
- 依赖关系；
- 每个 Task 的 Objective、Output、Validation、Guidance 和 Steps；
- Dependency Graph。

Manager 用 `Plan` 派发任务。Worker 不直接读取 `Plan`。

### 2.3 Rules

`{RULES_FILE}` 记录所有 Worker 都要遵守的执行规则。

Rules 必须自包含，不能写“参考 Spec”或“参考 Plan”。因为 Worker 会直接读取 Rules，但不会读取协调层文档。

---

## 3. 拆解原则

### 3.1 Domains

按专业领域或心智模型划分，例如 frontend、backend、data、QA、infra。

领域不同且上下文差异大时拆开；领域强耦合且分开会增加误解时合并。

### 3.2 Stages

Stage 是顺序里程碑。Stage N+1 默认在 Stage N 完成后开始。

并行发生在同一个 Stage 内的不同 Tasks，而不是多个 Stage 并行。

### 3.3 Tasks

每个 Task 应该：

- 属于一个 Worker；
- 产生明确交付物；
- 可以独立验证；
- 有清晰依赖；
- 不跨越过多领域；
- 不包含无关重构。

如果一个 Task 需要独立验收多个结果，考虑拆分。如果拆得太碎只会增加协调成本，考虑合并。

### 3.4 Validation

每个 Task 都必须有具体可执行验收标准：

- 测试命令；
- 构建或 lint；
- 文件和产物检查；
- 行为验证；
- 需要用户参与的审查点。

不要写“质量良好”“实现正确”这种不可执行标准。

---

## 4. Spec 生成

先在聊天中输出：

```markdown
**Spec Analysis:**
```

分析：

- 项目目标；
- 关键需求；
- 范围和非范围；
- 设计决策和约束；
- 权威材料；
- 工作区结构；
- 哪些内容属于 Spec，哪些属于 Plan 或 Rules。

然后读取 `.apm/spec.md`，写入完整 Spec。YAML frontmatter：

```yaml
---
title: <project name>
modified: Spec creation by the Planner.
---
```

写完后暂停，请用户审查。用户批准后才能进入 Plan。

---

## 5. Plan 生成

先在聊天中输出：

```markdown
**Plan Analysis:**
```

必须覆盖：

- **Domain Analysis:** 领域划分和 Worker 名称；
- **Stage Structure:** Stage 顺序和每个 Stage 交付物；
- **Stage N:** 每个 Stage 的 Tasks；
- **Dependency Analysis:** 依赖审计，特别是 cross-agent dependencies；
- **Pre-write Checks:** Task 是否粒度合适、验证是否明确、Worker 分配是否合理。

然后读取 `.apm/plan.md`，写入完整 Plan。YAML frontmatter：

```yaml
---
title: <project name>
modified: Plan creation by the Planner.
---
```

Plan 格式：

```markdown
# APM Plan

## Workers

| Worker | Domain | Description |
|--------|--------|-------------|

## Stages

| Stage | Name | Tasks | Agents |
|-------|------|-------|--------|

## Dependency Graph

```mermaid
graph TB
```

---

> **Notes:** 给 Manager 的工作结构观察。

## Stage 1: <Name>

### Task 1.1: <Title> - <Domain> Agent

* **Objective:** ...
* **Output:** ...
* **Validation:** ...
* **Guidance:** ...
* **Dependencies:** None

1. ...
2. ...
```

跨 Agent 依赖必须加粗，例如：

```markdown
* **Dependencies:** **Task 1.2 by Backend Agent**
```

写完后暂停，请用户审查。用户批准后才能进入 Rules。

---

## 6. Rules 生成

先在聊天中输出：

```markdown
**Rules Analysis:**
```

分析：

- 哪些规则适用于所有或大多数 Tasks；
- 哪些只是 Task-specific guidance，不应进入 Rules；
- 现有 `{RULES_FILE}` 中哪些内容需要保留或引用；
- 是否存在用户特别强调的执行纪律。

然后读取或创建 `{RULES_FILE}`，写入 APM_RULES block。

建议格式：

```markdown
<!-- APM_RULES_START -->

## APM Rules

### 通用执行规则

- ...

### 测试和验证

- ...

### 版本控制

- ...

<!-- APM_RULES_END -->
```

如果 `{RULES_FILE}` 已有内容，保留 APM_RULES block 外的所有内容。

写完后暂停，请用户审查。用户批准后，声明 Work Breakdown 完成，并回到 Planner 启动命令中的规划阶段完成步骤。

---

## 7. 常见错误

- Spec 写成任务列表；
- Plan 缺少验证标准；
- Worker 过多导致协调成本过高；
- Task 跨多个领域；
- Rules 引用 Spec 或 Plan；
- 没有让用户逐份批准；
- Dependency Graph 和 Task Dependencies 不一致。

---

**Guide 结束**

