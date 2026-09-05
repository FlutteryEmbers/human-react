# Human ReAct Tasks

本目录包含五个可直接用于现代 Agent harness 的 task prompt。Task 由 Human 按“本轮需要得到什么结果”选择，不按最终项目目标或固定 workflow stage 选择。

共享运行语义见 [`../core.md`](../core.md)，task 之间的关系见 [`../loop.md`](../loop.md)，输出格式见 [`../templates/`](../templates/)。

## Result-oriented Taxonomy

```text
Orient changes Human understanding.
Review establishes evidence-backed judgment.
Shape constructs a Decision Space.
Plan forms a Required Delta and Execution Model when needed.
Build changes or confirms Reality and returns verified feedback.
```

| Human 当前需要的结果 | Task | State Transition |
| --- | --- | --- |
| 理解背景、结构、机制或不同视角 | [`orient`](orient.md) | Subject + Learning Need + Available Context → Scoped Explanatory Model |
| 判断现状、gap、原因或适用性 | [`review`](review.md) | Existing Target → Evidence Context |
| 对齐语义并构造未承诺的方向空间 | [`shape`](shape.md) | Human Expression + System Context + Agent Modeling Contribution → Decision Space |
| 判断必要修改并形成可执行方案 | [`plan`](plan.md) | Decision Space + Evidence + Delegation + Current State → Required Delta + Execution Model when needed |
| 确认或改变现实并验证结果 | [`build`](build.md) | Requested Outcome + Current Reality + Authorized Change → Verified Reality + Actual Change when required + Loop Closure Observation |

搜索、阅读、追踪、局部 diagnosis、工具调用和验证是 task 内部能力，不是额外 task。Human 可以跳过或重复任意 task；选择 task 不证明其他 task 已完成，也不会扩大 user prompt 的 scope 或权限。

典型区分：

| Request | Task |
| --- | --- |
| 解释调用链怎样工作 | Orient |
| 判断调用链为什么没有形成预期 journey | Review |
| 讨论调用链应该怎样重新建模 | Shape |
| 给出必要修改面和验证方案 | Plan |
| 修改代码或确认现状并验证 | Build |

## Task Prompt Contract

每个 task prompt 保持相同骨架：

```markdown
# Task: <name>

> <state transformation>

## Shared Contract

## Responsibility

## Working Policy

## Boundaries

## Handback

## Complete When

## Result Projection
```

- `Shared Contract`：保证单个 prompt 可以脱离 loader 独立使用；
- `Responsibility`：定义本轮唯一结果类型；
- `Working Policy`：当前 task 的行为和判断机制；
- `Boundaries`：不可越过的红线；
- `Handback`：停止并把控制权返回 Human 的条件；
- `Complete When`：结果、证据和边界的内部核对；
- `Result Projection`：链接对应 chat projection，不复制格式规则。

Working Policy 是 task-specific 行为的唯一详细定义。Boundaries、Handback 和 Complete When 不应反向复述完整过程。Task prompt 可以简短重述当前 task 所需的共享原则，但不得建立与 [`core.md`](../core.md) 平行的定义。

## Shared Runtime Boundary

所有 task 都遵循以下最小边界：

- 当前 user prompt 决定实际 target、scope 和权限；
- task 名称只决定结果类型，不产生额外授权；
- Agent 自主完成当前结果所需的局部 micro ReAct；
- Agent 不改变目标、不显著扩大 scope，也不替 Human 作出重要取舍；
- material reconciliation 对 Human 可见；
- 触及目标、scope、权限或重要风险边界时 Handback；
- task 完成后不自动进入下一 task。

详细共享原则只在 [Human ReAct Core](../core.md) 定义：

- [Epistemic Separation](../core.md#epistemic-separation)；
- [Bounded Reconciliation](../core.md#bounded-reconciliation)；
- [Commitment Grounding](../core.md#commitment-grounding)；
- [Delta Grounding](../core.md#delta-grounding)；
- [Observable Boundary Gate](../core.md#observable-boundary-gate)；
- [Compatibility by Exception](../core.md#compatibility-by-exception)；
- [Task Closure and Handback](../core.md#task-closure-and-handback)。

## Optional Lens Composition

Human 可以为当前 task 显式附加一个适用的 [Lens](../lenses/)。组合遵循 `Task Contract + Human-selected Lens + resolved direct dependencies → specialized micro ReAct`：普通 Lens 只补充关注角度、证据期待或项目坐标系；带 `effects` 的 Lens 由 Human 显式选择后，只获得 metadata 声明的 protocol-owned sidecar authorization。结果仍按当前 task 的 template 投影，Lens 不增加 task scope、Status 或路由。未显式提供 Lens 时，五个 task 的行为、输出和工具自治保持不变。

## Task Index

### Orient

形成面向 Human understanding 的 Scoped Explanatory Model。它解释对象，不形成 correctness verdict、Decision Space、Execution Model 或现实修改。完整规则见 [`orient.md`](orient.md)。

### Review

围绕已有 target 形成 evidence-backed finding、gap、diagnosis、fitness judgment 或可靠的不确定性边界。它不修改 target。完整规则见 [`review.md`](review.md)。

### Shape

对齐 Human 表达与系统语义，构造 Candidate、Constraint、Pressure Point 和 Human-decision exception 组成的 Decision Space。它不形成唯一实施承诺。完整规则见 [`shape.md`](shape.md)。

### Plan

根据 Current State 判断 Required Delta，在 delegation 内关闭 means-level choice，并在需要修改时形成 Change Surface、Execution Model 和 Verification。它不修改现实。完整规则见 [`plan.md`](plan.md)。

### Build

依据当前 Build request 和最新 Reality 确认剩余 delta，实施必要且已授权的现实干预，并以 Verification 和 Loop Closure Observation 返回 Human。完整规则见 [`build.md`](build.md)。

## Selection And Handback

Human 不需要预测完整流程，只需选择本轮结果。Agent 若发现请求的核心结果已经属于另一种 task，应返回当前 task 已形成的有效内容和边界，而不是静默改换结果类型。

Task 可以建议少量有信息价值的可能方向，但不能自动路由、调用下一 task 或把建议变成授权。`complete / partial / blocked` 只描述当前 task 请求的完成度，不评价整个项目，也不表示下一 task readiness。
