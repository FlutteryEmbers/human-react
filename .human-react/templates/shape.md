# Shape Result Projection

Shape 结果是面向 Human 和当前 conversation 的 Modeling Checkpoint。它应让 Human 快速看见 Agent 如何理解问题、本轮改变了什么、候选之间真正的区别，以及哪些例外选择必须由 Human 决定。

使用 [`common.md`](common.md) 定义的公共 Task Result。`Outcome` 直接说明本轮完成的语义对齐、模型修正或 Decision Space update，不用“已完成 Shape”之类过程描述代替结果。

## Status

- `complete`：当前请求要求的共享理解或 Decision Space update 已形成；
- `partial`：已形成有用的局部模型，但关键歧义、范围或必须由 Human 作出的 decision 仍限制当前结果；
- `blocked`：无法识别 Human Anchor，或缺少必须由 Human 提供的语义、范围、权限或风险选择，且当前无法形成有用局部结果。

`Status` 只描述本轮 Shape 的完成度。它不表示 Decision Space 已收敛、Plan 已 ready 或 Build 已获得授权。

## Context Projection

`Context` 默认只投影本轮新产生且有决策价值的内容。以下 section 按需组合，不得为填满格式而重复 conversation。

### Focus

`Focus` 用来暴露 Human 表达到系统语义的翻译结果。

```markdown
### Focus

- Human Anchor: <Human 真正关心、不应被 Agent 静默替换的结果>
- Current Take: <Agent 对问题及其与当前系统关系的可修正解释>
- Alternative Read: <只在存在 materially different 的另一种解读时使用>
```

- `Human Anchor` 只在请求存在重构、歧义、机制与目标混合，或需要 consolidated view 时显式投影；简单连续回合不强制重复。
- `Current Take` 不是 Human Intent、shared truth、Fact 或 Decision。后续短轮只在它实质变化或需要 Human 纠正时再次投影。
- `Alternative Read` 必须是会导致不同目标、范围、系统映射或候选方向的实质差异，不列出低价值的语言变体。

### Decision Space

Decision Space 是 Shape Context 的主体。根据当前 focus 选择最小充分结构：

```markdown
### Decision Space

- [Fact] <由当前项目 evidence 支持的事实>
- [Constraint] <Human 或项目明确要求遵守的规范边界>
- [Decision] <Human 已作出的选择>
- [Candidate] <能作为整体被接受、拒绝或比较的最小未承诺 proposal>
- [Conditional] <如果显式 premise 成立则会如何，不接受 premise 为事实>
- Pressure Point: <候选之间真正会改变结果的冲突或取舍>
- Decision Criteria: <区分候选的标准>
- [Open] <必须由 Human 决定的问题或选择>
  - Human Decision Required: <涉及的目标、价值、scope、contract、risk 或 authority 边界>
```

不需要上述所有类型同时出现。Human 对事实的确认不替代项目 evidence，系统 evidence 也不创建 Human preference、Decision 或 authorization。未经 Human 确认的 Agent contribution 不得使用 `[Decision]`；领域知识的吸收状态使用现有 `[Candidate]`、`[Decision]`、`[Deferred]` 和 `[Constraint]` 表达，不增加独立必填字段。

Candidate 的粒度按 decision unit 判断，不按段落、字段或知识确定度判断：

- 同一方案中需要一起接受的职责、结构和约束放在一个 `[Candidate]` 下；
- 只有可独立选择、相互替代或需要分别确认时才并列多个 Candidate；
- 互斥 Candidate 需要 Pressure Point，并在标准确实能改变选择时给出 Decision Criteria；
- 依赖或兼容关系只在影响 Human 判断时用简短文字说明，不创建固定 relationship 字段或 enum；
- 未证实 fact 使用 `[Assumption]` 或 Unknown，条件推演使用 `[Conditional]`，不因它们未确认就改写为 Candidate。

Candidate 集合默认是当前 focus 下有用但非穷尽的集合。未被 `Rejected` 或 `Deferred` 的普通 means-level Candidate，在 Human 显式发起 Plan 后可以被 Plan 考虑和关闭，不需要 `Owner` 或 `Delegable` 字段；这不表示 Human 已接受 Candidate，也不产生 Build authorization。只有会改变 Desired Effect、产品或领域语义、scope、external contract、risk acceptance 或 authority 的例外选择，才使用 `Human Decision Required` 并同时进入 Human Attention。

Agent 提出的 Decision Criteria 是建模贡献。只有 evidence-backed constraint 或 Human 已确认的 preference / constraint 才能约束后续 decision；当 criteria 的来源会改变选择时，用简短自然语言说明，不增加固定 provenance 字段。高影响选择存在 material framing risk 时，说明候选集合边界或补充 materially different alternative；普通结果不输出完整性声明。

#### Compatibility by Exception

Compatibility 只在 material Surface 存在或 preserve / break 会实质改变 contract、scope、成本、风险或 acceptance 时进入 Decision Space。它不创建固定 `Compatibility` section，而是复用现有标签：

```markdown
- [Fact] Compatibility Surface: <已知外部消费者、接口、格式或 observable behavior>
- [Constraint] Compatibility Boundary: <必须 preserve 的内容>
  - Basis: <Human、项目规范或 evidence>
```

Human 明确接受 breaking change 时使用 `[Decision]`；尚未承诺的 boundary proposal 使用 `[Candidate]`。如果 Boundary 会改变 contract、scope、成本、风险或 acceptance，则使用：

```markdown
- [Open] Compatibility Boundary: <需要决定的 preserve / break 边界>
  - Human Decision Required: <该选择的实质影响>
```

Surface 无法确认时保持 Unknown，不推断不存在消费者。Compatibility Mechanism 可以作为 means-level Candidate，但不得在 Shape 中写成已选实施方案。没有 material compatibility 内容时省略整个 trace，不生成空兼容占位。

### Model Delta

多轮 Shape 默认只返回本轮有意义的变化：

```markdown
### Model Delta

- Added: <新增的 context、candidate、criterion 或 open decision>
- Changed: <被 Human correction、系统 evidence 或新 context 修正的内容>
- Rejected: <已被证据或 Human 明确排除的理解或候选>
```

首次简单 Shape、无实质变化，或 `Outcome` 已足以表达 delta 时省略整个 section。不输出“Added: none”之类空字段。

### Reconciliation

当 Human language、system meaning、Current Take、constraint 或 candidate model 之间的冲突实质影响共享工作模型时，按公共 [material reconciliation disclosure](common.md#material-reconciliation-disclosure) 投影：

- 已通过 semantic grounding 或局部 system evidence 消除的误解可以是 `resolved`；
- 为继续建模而暂时采用的 working interpretation 使用 `provisional`，不自动成为 evidence-backed Fact 或 Human normative commitment；
- 两个都有信息价值的候选使用 `preserved`，并在 `Effect` 中将其映射为 Candidate、Pressure Point 或 Open Decision；
- 如果冲突需要 Human decision，它同时进入 `Human Attention`，不能通过 Reconciliation 静默关闭。

### Possible Next Move

只在新的继续方向有实质信息价值时投影，通常不超过 3 项：

```markdown
### Possible Next Move

- <可能行动>，用于 <将关闭的 unknown、decision 或方向>。
  - Possible task: orient | review | shape | plan | build
```

`Possible task` 只在映射明确且有助于 Human 选择时使用。Possible Next Move 不是 task routing、默认路径或授权；投影后立即 Handback。

## Human Attention

只放置必须由 Human 纠正、选择、授权或承担风险的事项。可以在当前 Shape scope 内通过 context 或局部检查消除的普通未知，不应被写成 Human blocker。

## Compact Shape

一份典型结果可以是：

```markdown
## Task Result

- Task: shape
- Status: complete | partial | blocked
- Outcome: <本轮建立或修正的共享理解 / Decision Space>
- Human Attention: <必要的 Human correction/decision/authorization；没有则为 none>

### Context

### Focus

- Human Anchor: <必要时>
- Current Take: <当前可修正解释>
- Alternative Read: <存在实质歧义时>

### Decision Space

- [Fact] <evidence-backed context>
- [Constraint] <Human 或项目明确的规范边界>
- [Candidate] <一个可整体接受或拒绝的 proposal>
  - Includes: <需要一起被理解的组成部分；无需时省略>
- Pressure Point: <重要取舍>
- [Open] <需要 Human 决定时才投影>
  - Human Decision Required: <涉及的边界>

### Model Delta

- Added: <本轮新增>
- Changed: <本轮修正>
- Rejected: <本轮排除>

### Possible Next Move

- <有信息价值时才输出>
```

这是可裁剪的 projection shape，不是必填表单。普通短轮可以只输出公共头部和一个有价值的 Context block；只有 consolidated projection 才需要重建当前完整 Decision Space。
