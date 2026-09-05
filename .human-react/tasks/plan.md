# Task: plan

> Decision Space + Evidence + Delegation + Current State → Required Delta + Execution Model when needed

## Shared Contract

- 当前 user prompt 决定 target、scope 和权限；task 名称不增加授权；
- 为形成当前结果所需的局部观察、推理和工具调用可以自主完成；
- material reconciliation 必须可见；不改变目标、不显著扩大 scope、不替 Human 作重要取舍；
- 触及边界时 Handback，完成后不自动进入 Build。

## Responsibility

从当前请求、Decision Space、Evidence、Delegation 和 Current State 判断 Required Delta；需要现实修改时，在委托内关闭 means-level choice，形成一致、可执行且可验证的 Execution Model。Plan 不要求 Shape 或 Review artifact，也不修改现实对象。

## Working Policy

- 提取 Target Outcome、evidence、Constraint、Human Decision、Candidate、criteria、Assumption 和 Current Delegation，只保留会改变路径、修改面或验证的 context。
- 依赖 repo reality 时进行最小充分检查，确认 Current State、相关模块、现有模式、依赖和 verification entrypoint。能发现的事实不机械交还 Human。
- Human 发起 Plan，默认委托 Agent 关闭既有 goal、scope、Constraint 和 risk boundary 内未被 Rejected 或 Deferred 的 means-level choice。使用 `Resolved Choice + Basis` 表达，不冒充 `[Decision]`；改变 Human value、产品或领域语义、scope、external contract、Compatibility Boundary、权限或重要风险时要求 Human 决定。
- 使用 `Requested State / Effect - Observed Current State = Required Delta`。Current State 未知且会实质改变修改面时使用 Assumption、返回 `partial` 或 Handback，不虚构 gap。
- 只有实际存在的 Required Delta 才生成 Change Surface。每个 target 必须有 observable unmet condition、可追溯到当前请求或不可缺少的 companion change，并构成最小充分干预；companion target 的 Reason 说明因果必要性。已满足的目标、preserve constraint 和受影响但无需修改的对象不进入 Change Surface。
- 对 evidence 已指向、且可能改变 Change Surface、Verification、risk 或 Human decision 的 coupling 做 bounded impact inquiry。调查在更多信息不再可能改变这些结果时停止，不构造完整 Effect Surface 或探索推测性消费者。
- 存在 Required Delta 时，以结果为单位组织 work packages、必要依赖和顺序；没有 delta 时用 Outcome 与 Verification 说明当前状态，不制造 no-op Execution Model。
- Success Criteria 定义 Verification Obligation；Checks 是当前 evidence 下的推荐方法，Build 可用 coverage 等价的方法替换。只有 Human 将特定平台、runner、command 或环境纳入 acceptance 时才固定方法。
- Material coupling 使用 Change Surface Reason、Risks / Stop Conditions 和 Verification 表达。Compatibility Boundary 已建立时，Plan 可在其范围内选择 Mechanism；明显改变已知 external contract 但 Boundary 未建立时返回 `partial`，不把缺少 preserve Constraint 当成 breaking authorization。
- 对 requested target、scope、delegation、Candidate、Boundary、strategy、dependency 和 verification 冲突进行 Bounded Reconciliation。实质改写 Shape context、inferred Change Surface 或 Human request 时披露。
- 多轮 Plan 可只返回变化部分；Change Surface 改变时必须重建完整当前修改面，并同步受影响的 Execution Model、Scope 和 Verification。

## Boundaries

- 不重新定义 Target Outcome、产品或领域语义；
- 不用 Shape Candidate、Agent Criteria、Allowed Scope、traceability 或 preserve constraint 单独证明修改必要或授权；
- 不修改现实，不展开无边界 diagnosis、Effect Surface、兼容考古或体系补全；
- 不生成 routing、persistence、external handoff 或默认兼容政策。

## Handback

Target Outcome 或关键 acceptance 无法识别，可靠方案需要新 scope、权限、Compatibility Boundary、重要风险接受或 Human-owned semantic choice，或主要事实只能通过开放式取证获得时 Handback。已有局部 Required Delta 或 Execution Model 时返回 `partial`；证据证明无需修改时可以 `complete`。

## Complete When

- Required Delta 已根据可观察 Current State 建立；无 delta 时 Outcome 和 Verification 已说明理由；
- Change Surface、Execution Model、Scope、Verification 和已知 material coupling 一致；
- means-level choice 已关闭，Human-owned decision、Reconciliation 和 residual risk 已按需可见；
- 没有修改现实，也没有产生 Build authorization 或自动后续行动。

`complete` 表示 Required Delta 判断和必要 Execution Model 已足以执行与验证，不表示 Human 接受全部推理或授权 Build。

## Result Projection

使用 [`../templates/plan.md`](../templates/plan.md)，只投影结果，不输出 planning 流水账或隐藏推理。
