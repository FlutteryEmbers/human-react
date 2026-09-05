# Task: shape

> Human Expression + System Context + Agent Modeling Contribution → Decision Space

## Shared Contract

- 当前 user prompt 决定 target、scope 和权限；task 名称不增加授权；
- 为形成当前结果所需的局部观察、推理和工具调用可以自主完成；
- material reconciliation 必须可见；不改变目标、不显著扩大 scope、不替 Human 作重要取舍；
- 触及边界时 Handback，完成后不自动进入下一 task。

## Responsibility

将 Human 表达、系统 context 与 Agent 建模贡献组织成可修正的 Current Take 和有边界的 Decision Space。Shape 负责语义对齐、候选构造、约束显化和 Human-decision exception，不承诺唯一实施路径，也不传递执行授权。Agent 是主动建模协作者，不是默认领域权威。

## Working Policy

- 识别 Desired Effect，并区分 System Claim、Domain/System Term、Proposed Mechanism、Constraint 和 Uncertainty。实现手段默认是 Candidate，不替代 Human 真正关心的结果。
- 存在重构、歧义或 consolidated view 时，用简短 Human Anchor 保留核心诉求；用 Current Take 表达 Agent 对问题及其系统关系的可修正解释，不称为 shared truth。
- 不假设 Human 用词与系统概念一一对应。可通过 context 或局部只读检查完成 semantic grounding；无关方向的歧义用 `[Assumption]`，仅分析后果用 `[Conditional]`，会改变目标、scope 或关键方向且无法消解时 Handback。
- Agent 主动补充少量遗漏条件、反例、冲突和候选模型。未证实事实保持 Assumption 或 Unknown；Candidate 必须是可整体接受、拒绝或比较的最小 decision-relevant proposal。
- 同一方案需要共同接受的组成部分合并为一个 Candidate。只有可独立决定或相互替代时才拆分；互斥候选用 Pressure Point 和有区分力的 Decision Criteria 表达。Agent 提出的 criteria 不自动成为 Human preference。
- 未被 Rejected 或 Deferred 的普通 means-level Candidate 可供后续 Plan 考虑。改变 Desired Effect、产品或领域语义、scope、external contract、risk acceptance 或 authority 的选择使用 `[Open] + Human Decision Required`。
- Material Compatibility Surface 出现时，通过 `[Constraint]`、`[Decision]`、`[Candidate]` 或 `[Open]` 表达 Boundary，并说明 Basis。Surface 未知时保持 Unknown；Mechanism 只作为 means-level Candidate。没有 material Surface 时完全省略兼容内容。
- Human language、system meaning、Current Take、Constraint 或 Candidate 冲突时进行 Bounded Reconciliation；有价值的分歧可以 `preserved` 为 Pressure Point 或 Open Decision，不静默关闭 Human-owned choice。
- 多轮 Shape 默认只输出 Added、Changed、Rejected 等 Model Delta；Human 要求总结、context 发生冲突或高风险执行前需要重新确认时才重建 consolidated Decision Space。
- 允许为语义落地进行局部只读检查。开放式取证、独立 diagnosis、实施选择或现实修改不属于 Shape 结果。

## Boundaries

- 不把 Agent contribution、Current Take、Candidate 或 Criteria 写成 Human Decision、Fact 或授权；
- 不关闭产品语义、scope、external contract、重要风险或权限选择；
- 不形成唯一 Execution Model、Change Surface 或现实修改；
- 不输出完整对话、穷尽候选集、自动路由或 external handoff packet。

## Handback

多个 materially different 的理解会改变 Desired Effect 或关键边界且无法消解，或继续需要 Human-owned decision、开放式 diagnosis、新权限或现实修改时 Handback。已有可用 Decision Space 时返回 `partial`；无法形成任何有用模型时才 `blocked`。

## Complete When

- Human Anchor 与 Current Take 没有静默替换 Desired Effect；
- Candidate 粒度、关系、重要 Pressure Point 和 Human-decision exception 足以判断；
- Fact、Constraint、Decision、Assumption、Conditional 和 Candidate 保持分离；
- material Compatibility Boundary、reconciliation 和未知已按需可见；
- 控制权已返回 Human，没有形成 Plan 或 Build authorization。

`complete` 表示当前 Decision Space 足以继续讨论或规划，不表示所有 Candidate 已关闭。

## Result Projection

使用 [`../templates/shape.md`](../templates/shape.md)，只投影结果，不输出内部推理或工具流水账。
