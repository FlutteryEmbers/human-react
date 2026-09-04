# Plan Result Projection

Plan 结果是面向 Human 和当前 conversation 的 Execution Checkpoint。它应让 Human 在第一屏看到选择的路径和作为 `Planned Change` 的实际修改面，并为后续独立的 Build authorization 提供清晰边界。

使用 [`common.md`](common.md) 定义的公共 Task Result。`Outcome` 直接说明 Target Outcome、Chosen Approach 和预期效果，不另生成重复的 summary dashboard 或 handoff packet。

## Status

- `complete`：当前 Execution Model 已足以在已知授权内执行和验证；
- `partial`：已形成有用的局部 Execution Model，但剩余 `Human Decision Required` 或 evidence gap 限制其完整性；
- `blocked`：无法识别 Target Outcome、必要边界或可靠 working basis，且当前无法形成任何有用方案。

`Status` 只表示当前 Plan 请求的完成度。`complete` 不表示 Build 已获得授权，`partial` 也不要求删除已可用的 Change Surface 或 work packages。

## Context Projection

`Context` 优先投影当前 Plan 新产生的选择、修改面和执行模型。简单 Plan 省略无信息价值的 section，但只要输出 Execution Model，就必须输出 Change Surface。

### Planning Basis

只在目标复杂、context 容易漂移，或关键 Human Decision、Constraint、Assumption 会改变执行时投影：

```markdown
### Planning Basis

- Target Outcome: <执行后应该成立的状态>
- [Decision] <Human 已明确作出的决定>
- [Constraint] <必须遵守的边界>
- [Assumption] <为继续规划而暂时采用的未证实前提>
```

`[Decision]` 只表示 Human 明确决定。Agent 在 Current Delegation 内关闭的 choice 放在 Chosen Approach，不使用该标签。Shape 的 Candidate、Open Decision 或分类不是 authorization source。

已建立的 Compatibility Boundary 在 material 时作为 `[Constraint]` 或 `[Decision]` 放入 Planning Basis，并用简短 Basis 说明来源。没有 material Surface 时不生成兼容字段；未知或推测性的消费者也不触发占位内容。

### Chosen Approach

说明单一整体路径，以及对执行有实质影响的授权内选择：

```markdown
### Chosen Approach

- Approach: <整体实施路径>
- Resolved Choice: <Agent 在 Current Delegation 内关闭的 choice>
  - Basis: <已确认标准、evidence 或工程约束；authority 不明显时同时说明为何属于 delegated means>
```

没有需要单独披露的 Resolved Choice 时只保留 `Approach`。不列出对结果无影响的局部实现偏好。

Compatibility Boundary 已建立时，adapter、migration、dual-read、alias、cutover 或直接 breaking implementation 等 Compatibility Mechanism 可以作为 `Resolved Choice`，其 Basis 必须说明它如何满足该 Boundary。Plan 不通过选择 Mechanism 创建、放宽或取消 Boundary。

### Change Surface

Change Surface 是 Human 检查实际计划修改面的核心视图。它表达当前 Execution Model 准备改变什么，是 `Planned Change`；不表示变化已经发生，不构成 Build Authorization，也不表达全部被允许修改的范围。

小型计划使用紧凑列表：

```markdown
### Change Surface

- `<target>`: <intended change> — <reason>
```

多修改面计划使用：

```markdown
### Change Surface

| Target | Intended Change | Reason |
| --- | --- | --- |
| <module, behavior, file group or interface> | <准备改变什么> | <为什么需要> |
```

Target 选择 Human 能判断影响的最小稳定粒度；不为显得具体而编造未检查的文件路径。每个 target 必须直接来自当前请求，或是完成已授权结果不可缺少的最小附带修改。无法建立这种 traceability 的相邻清理、体系对齐或“顺便完善”不得进入 Change Surface。文件清单、Scope 和 work package 不能代替 Change Surface。

### Execution Model

使用有结果的 work packages 和必要依赖：

```markdown
### Execution Model

1. <work package / state transition>
   - Targets: <相关对象>
   - Depends on: <仅在存在实质依赖时>
   - Result: <该包完成后什么应该成立>
```

顺序只在有依赖时表示时序；可并行的 work packages 不伪造线性依赖。不展开工具调用、逐行编辑或可由 Build 在既定边界内自主决定的微步骤。

### Scope

只在存在重要授权边界时投影：

```markdown
### Scope

- Allowed Changes: <可修改的边界>
- Do Not Touch: <必须保持不变的对象或行为>
- Out of Scope: <已明确排除的工作>
```

空字段直接省略。`Allowed Changes` 不扩大 user prompt 的授权，`Out of Scope` 不收集无关的未来工作。

### Verification

`complete` Plan 必须包含：

```markdown
### Verification

- Success Criteria: <执行后应成立的可观察结果>
- Checks: <推荐的 targeted test, static check, smoke check 或 manual check>
- Fallback: <主要验证不可用或不充分时才使用>
- Residual Risk: <Fallback 后仍存在的实质风险>
```

Success Criteria 定义 Verification Obligation；Checks 是当前 planning evidence 下推荐的 Verification Method，必须有能力证明 Target Outcome，而不只是证明命令能运行。Build 可以根据 Actual Environment 替换 shell syntax、path、runner 或其他 evidence coverage 等价的方法。只有 Human 明确将特定 platform、runner、command 或 environment 纳入 acceptance / Constraint 时，该方法才是不可静默替换的计划边界。Fallback 和 Residual Risk 无内容时省略，不输出 `none`。

Planned Change 涉及 material Compatibility Surface 时，Checks 同时验证 Compatibility Mechanism 是否满足已建立的 Boundary；不新增 compatibility-specific verification section。

### Risks / Stop Conditions

只投影会改变实施、授权或失败处理的内容：

```markdown
### Risks / Stop Conditions

- [Risk] <会影响执行或结果判断的风险>
- Stop Condition: <必须停止并 Handback，而不是自行扩大 scope 的情况>
```

### Reconciliation

requested target、scope、delegation、inferred Change Surface、Compatibility Boundary、实施策略、技术约束、依赖、顺序或 verification context 之间的冲突实质改变 Chosen Approach、Change Surface、Execution Model、Scope 或 Verification 时，按公共 [material reconciliation disclosure](common.md#material-reconciliation-disclosure) 投影。

- Plan 可以在 Current Delegation 内选择 working basis 并继续；
- working basis 不创建新的 Human intent、产品语义、scope、兼容政策或风险承诺；
- Plan 降级、排除、重分类或实质改写 Shape Candidate / Criteria / Compatibility Boundary，或者对 user request 采用会改变修改面的解释时，属于 material reconciliation；
- 需要 Human 决定的冲突在 `Effect / Residual` 中说明，并同时进入 `Human Attention`。

## Multi-round Projection

同一 conversation 中的局部修订可以只投影变化的 Chosen Approach、Execution Model、Scope 或 Verification。如果 Change Surface 发生变化，必须重建完整当前 Change Surface，并同步重投影受影响的 section。

Plan 不生成 `Confirmed` 字段。Human 后续明确发起 Build 时，当前 execution request 决定 Change Surface 中哪些部分成为 `Authorized Change`；它不表示 Human 接受 Plan 的全部事实判断、理由或未被当前请求覆盖的修改面。

## Human Attention

只放置必须由 Human 确定的 Target Outcome、value、产品或领域语义、scope、external contract、Compatibility Boundary、权限或重要风险接受。Planned Change 明显改变已知 external contract 而 Boundary 尚未建立时，Plan 使用 `partial` 并在这里说明 preserve / break 的实质影响。可由 Agent 在已建立 Boundary 和 Current Delegation 内关闭的 Compatibility Mechanism 或其他技术选择不应被写成 Human blocker。

## Compact Plan

```markdown
## Task Result

- Task: plan
- Status: complete | partial | blocked
- Outcome: <Target Outcome、Chosen Approach 与预期效果>
- Human Attention: <必要的 Human decision/authorization；没有则为 none>

### Context

### Chosen Approach

- Approach: <单一整体路径>
- Resolved Choice: <重要的授权内选择>
  - Basis: <选择依据>

### Change Surface

- `<target>`: <intended change> — <reason>

### Execution Model

1. <work package>
   - Result: <完成后应成立的状态>

### Verification

- Success Criteria: <可观察完成标准>
- Checks: <推荐的可执行验证方法>
```

这是可裁剪的 projection shape，不是必填表单。Planning Basis、Scope、Risks / Stop Conditions 和 Reconciliation 只在有实质内容时输出；不增加 routing、persistence、跨对话 handoff、独立兼容 section 或默认兼容字段。
