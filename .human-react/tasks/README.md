# Human ReAct Tasks

本目录包含五个可直接用于现代 Agent harness 的 task prompt。Human 显式选择 Task 表达本轮协作意图；所选 Task 对 prompt 有最高意图解释优先级，决定主要结果责任，不按动作措辞自动重选或进入固定 workflow stage。

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

辅助分析不是所有 task 共享的通用能力清单。每个 task 只使用自身 Responsibility、Working Policy 和 Boundaries 明确允许、且直接支持解释后请求的认知活动，不产生独立目标或新增权限。Human 可以跳过或重复任意 task；只有明确重新选择才改变当前及续轮 task，选择不证明前序工作已完成。

同一句 prompt 按所选 Task 形成不同工作请求；下表不作为自动路由规则：

| Selected Task + Prompt | Task-scoped Request |
| --- | --- |
| Orient + 评价这个设计好不好 | 解释相关机制、条件、设计理由与取舍，披露评价诉求的改写 |
| Review + 修复并提交 | 审查问题、修改必要性、影响和改善方向，披露未修改、未提交 |
| Review + 理解并评价 | 一份 Review 同时包含理解模型与评价 |
| Shape + 直接实现方案 A | 围绕 A 构造、检验和比较候选方向，披露未实施 |
| Plan + 修好这个问题 | 必要修改面、执行方案和验证要求，披露尚未实施 |
| Build + 仅检查目标是否成立，不修改 | 验证当前现实并交付，不制造变更 |

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
- `Responsibility`：定义所选 task 的主要结果责任与动作诉求的解释方向；
- `Working Policy`：当前 task 的行为和判断机制；
- `Boundaries`：不可越过的红线；
- `Handback`：对象、证据、重要选择或权限等真实阻碍要求返回 Human 的条件，不按措辞冲突触发；
- `Complete When`：结果、证据和边界的内部核对；
- `Result Projection`：链接对应 chat projection，不复制格式规则。

Working Policy 是 task-specific 行为的唯一详细定义。Boundaries、Handback 和 Complete When 不应反向复述完整过程。Task prompt 可以简短重述当前 task 所需的共享原则，但不得建立与 [`core.md`](../core.md) 平行的定义。

## Shared Runtime Boundary

所有 task 都遵循以下最小边界：

- 所选 task 决定意图解释与主要结果责任，prompt 提供对象、关注目标、具体约束和上下文；
- 保持所选 task，将冲突措辞解释为 task 内请求并直接完成，不因措辞冲突确认、切换或降低状态；
- 实质改写按公共 Request Interpretation 披露，不冒充 Human Decision 或实际动作；
- Agent 只进行当前 task 明确允许、且服务于主要结果的局部 micro ReAct，不从公共协议、Task 选择或改写取得额外能力或操作授权；
- Agent 不改变目标、不显著扩大 scope，也不替 Human 作出重要取舍；
- material reconciliation 对 Human 可见；
- 触及目标、scope、权限或重要风险边界时 Handback；
- task 完成后不自动进入下一 task。

详细共享原则只在 [Human ReAct Core](../core.md) 定义：

- [Task-scoped Request Interpretation](../core.md#task-scoped-request-interpretation)；
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

以 Scoped Explanatory Model 为主要结果，将优化、修复、评价诉求解释为原对象的机制、条件、设计理由与取舍问题，不承担整体 verdict 或实施承诺。完整规则见 [`orient.md`](orient.md)。

### Review

以有依据的审查 context 为主要结果，包含必要理解模型、finding、gap、diagnosis、影响和改善方向；修改、提交措辞转为审查问题，不修改 target。完整规则见 [`review.md`](review.md)。

### Shape

以 Decision Space 为主要结果，将采用、实施诉求解释为候选构造、可行性判断、比较及推荐；实现细节只作为候选可行性依据，不形成完整 Execution Model、正式 Change Surface、工作包、实施清单或现实授权。完整规则见 [`shape.md`](shape.md)。

### Plan

将实施诉求解释为 Required Delta、执行方案与验证要求，可补充诊断、比较及替换机制，在 delegation 内关闭技术选择；不执行现实修改。完整规则见 [`plan.md`](plan.md)。

### Build

围绕原对象与目标交付 verified reality，可内部完成必要理解、诊断、局部设计、规划和验收评价；只实施 Human 当前 prompt 明确要求或其无歧义引用且仍有效的既有委托所授权的必要动作，尊重具体限制。完整规则见 [`build.md`](build.md)。

## Selection And Handback

Human 选择 task 表达本轮意图，Agent 不从 prompt 动词重新猜测 task。即使措辞明确冲突，也先保留对象、关注目标和具体约束，将诉求解释为所选 task 可承担的工作并完成，在结果中披露重要改写与处理边界。续轮保持选择，只有 Human 明确重新选择才改变。

Task 与措辞冲突不是 Handback 条件；对象无法确定、关键 evidence 缺失、重要选择未决或 task 内必要权限不足时，保留有用且可安全完成的部分并披露真实阻碍。不能借改写取消操作限制、扩大 scope、创造事实或用泛泛输出代替具体问题。

Task 可以建议少量有信息价值的可能方向，但不能自动路由、调用下一 task 或把建议变成授权。`complete / partial / blocked` 按解释后的 task 内请求判断；未执行的原文动作被转为讨论对象时不单独降低状态，也不自动成为待办或下一轮任务。状态不评价整个项目或下一 task readiness。
