# APM {VERSION} - Task Review Guide

## 1. 适用角色

**Reading Agent:** Manager

本指南定义 Manager 如何审查 Worker 的 Task Report 和 Task Log，如何决定通过、返工、修改计划，以及如何维护 `Tracker` 和 `Memory Index`。

---

## 2. 审查标准

### 2.1 Task Log 审查

读取 Task Log 后，判断：

- `status` 是否和正文证据一致；
- `important_findings` 是否影响其他 Tasks、Spec、Plan 或 Rules；
- `compatibility_issues` 是否意味着集成风险、设计错误或计划错误；
- Validation 是否真的覆盖了 Task Prompt 的验收标准；
- Output 是否和 Objective 匹配。

如果状态、flags 和正文矛盾，视为需要调查。

### 2.2 Review Outcome

审查后选择：

- **通过**：Task 达成目标，验证充分，无需处理 flags。
- **Follow-up**：Worker 需要带着更清晰指令重试。
- **修改规划文档**：Spec、Plan 或 Rules 中的假设被证伪或需要补充。
- **新增任务**：已 Done 的任务发现后续缺陷，原任务保持 Done，创建新 Task 处理。

小范围修正可以由 Manager 直接处理。影响范围、设计方向、项目范围或多个 Tasks 时，必须请求用户确认。

### 2.3 规划文档修改

修改前先判断级联影响：

- Spec 变化可能影响 Plan；
- Plan 变化可能反映 Spec 不完整；
- Rules 通常相对独立，但可能影响所有 Workers。

允许 Manager 直接处理：

- 单个 Task 澄清；
- 添加遗漏依赖；
- 小型 Spec 补充；
- 小型 Rules 调整。

需要用户确认：

- 多个 Tasks 受影响；
- 范围扩大或缩小；
- 设计方向变化；
- 新增 Stage；
- 多个小变化叠加成重大变化。

### 2.4 并行协调

并行 Workers 的报告可能乱序到达。每处理一份报告后：

1. 完成审查；
2. 必要时 merge；
3. 更新 Tracker；
4. 重新评估 Ready Tasks；
5. 如果有可派发任务，在同一轮继续派发。

只有没有 Ready Tasks、需要用户决策，或等待其他 Worker 报告时才暂停。

### 2.5 Merge

成功审查后，按 Tracker 中的版本控制约定 merge feature branch。

基本流程：

```bash
git checkout <base-branch>
git merge <branch-name>
```

并行 worktree 完成后，先移除 worktree，再删除已 merge 分支。

如果出现复杂冲突，先基于 Spec、Plan 和 Task Log 判断；必要时使用 debug subagent 或请求用户决策。

---

## 3. 审查流程

### 3.1 Report Processing

用户运行 `apm-5-check-reports` 后：

1. 读取 `.apm/bus/<agent-slug>/report.md`。
2. 如果是 batch report，逐个处理已完成 Tasks；未开始 Tasks 回到调度池。
3. 检查是否为 Handoff 后的新 Worker。如果是，更新 Worker tracking 和 cross-agent overrides。
4. 检查是否发生 Recover 或 auto-compaction，写入 Worker notes。
5. 将 Worker 标记为空闲。

### 3.2 Task Log Review

1. 读取 Report 中的 `log_path`。
2. 用中文展示审查判断：
   - 状态是否可信；
   - 验证是否充分；
   - flags 是否需要处理；
   - 下一步是什么。
3. 进入 Review Outcome。

### 3.3 Outcome Handling

1. 如果通过，更新 Tracker。
2. 如果需要 follow-up，按 `{GUIDE_PATH:task-assignment}` 创建 Follow-Up Task Prompt。
3. 如果需要修改 Spec、Plan 或 Rules，先做影响评估，必要时请求用户确认。
4. 如果任务完成且分支需要 merge，执行 merge。
5. 重新评估下一步：
   - Stage 完成：写 Stage Summary；
   - 有 Ready Tasks：回到 Dispatch Assessment；
   - 没有 Ready Tasks 但有 active Workers：告诉用户等待哪份报告。

### 3.4 Stage Summary

当一个 Stage 所有 Tasks 都 Done 且分支已 merge：

1. 列出该 Stage 的 Task Logs。
2. 判断是否需要整体 Stage verification。
3. 把 working notes 归类：
   - 对后续有长期影响的，写入 Memory Notes；
   - 只属于本 Stage 历史的，写入 Stage Summary。
4. 追加到 `.apm/memory/index.md`。

---

## 4. Tracker 格式

Task Tracking：

```markdown
**Stage 1:**

| Task | Status | Agent | Branch |
|------|--------|-------|--------|
| 1.1 | Done | frontend-agent | |
| 1.2 | Active | backend-agent | feat/api-auth |
| 1.3 | Waiting: 1.2 | frontend-agent | |
```

状态：

- `Waiting: N.M`
- `Ready`
- `Active`
- `Done`

Worker Tracking：

```markdown
| Agent | Instance | Notes |
|-------|----------|-------|
| frontend-agent | 2 | Handoff after Stage 1 |
```

Cross-Agent Overrides：

```markdown
**Cross-Agent Overrides:**
- frontend-agent: Tasks 1.1, 1.3 (pre-Handoff) - treat as cross-agent
```

---

## 5. Index 格式

`.apm/memory/index.md` 包含：

- `## Memory Notes`
- `## Stage Summaries`

Stage Summary 建议结构：

```markdown
### Stage <N>: <Name>

<Stage 结果、参与 Workers、主要交付物、关键发现、风险和后续影响。>

**Task Logs:**
- `.apm/memory/stage-<NN>/task-<NN>-<MM>.log.md`
```

---

## 6. 常见错误

- 只看 Report，不读 Task Log；
- Worker 标记 Success 就直接 Done；
- flags 为 true 但不调查；
- follow-up 仍复制原任务，不补充调查结论；
- Stage 完成后不做必要的整体验证；
- merge 前就派发依赖任务；
- Handoff 后不更新 dependency classification。

---

**Guide 结束**

