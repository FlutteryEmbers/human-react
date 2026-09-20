# Shape Result Projection

Shape 返回可修正的 Modeling Checkpoint。使用 [`common.md`](common.md)；Outcome 只说明本轮最重要的语义或 Decision Space 变化，不使用过程描述。

## Status

- `complete`：本轮需要的 working model 或 Decision Space 已形成；
- `partial`：已有有用模型，但关键歧义或 Human-owned decision 仍限制结果；
- `blocked`：无法识别 Human Anchor 或必要边界，且不能形成任何有用模型。

Status 按 Shape 下解释后的候选请求判断，实质改写遵循公共 Request Interpretation；原文实现动作未执行不单独降低状态。Status 不表示候选已收敛、Plan ready 或 Build 已授权。

## Default Projection

首次或 consolidated Shape 在公共外壳内使用 Decision Space。单议题短结果可以省略编号；此最小示例不要求生成其他可选字段：

```markdown
## Task Result

- Task: shape
- Status: complete
- Outcome: <本轮建立或修正的模型>
- Human Attention: none

### Context

#### Decision Space

##### <议题：需要回答或决定什么>

- [Candidate] <提案，以及紧邻的收益、代价、限制与依据>
```

议题是组织单位，Candidate 是可整体接受、拒绝或修改的最小 decision unit。需要共同接受的组成部分合并，独立提案可以拆分；可以只有一个提案或暂时只有澄清后的问题，不强造选项。`[Deferred]` 表示当前明确不吸收，不等于 Rejected。相关 Fact、Constraint、Decision、Assumption 与 Open 放在所属议题内；只有 Human 明确接受才能标为 Decision。

## Issue Attribution And Choice

- 多议题必须使用 `Q1`、`Q2` 等稳定编号与描述性标题；候选使用 `Q1-A` 等议题内编号。单议题且无需跨轮引用时可省略编号；开始需要引用时分配编号并沿用。
- 来源于 Review 时必须用 `Source` 引用 finding 编号或短标题；允许一个议题关联多个 finding，或一个 finding 拆成多个议题。直接 Shape 不要求 Review 或空 Source。
- 同题存在多个候选时必须说明替代、可组合或依赖关系，可紧邻候选说明。收益、代价、限制、可行性依据和必要背景紧邻候选；不另开教学 section。
- `Comparison` 仅在逐项说明不足以判断差异时出现，只解释已列明候选。新提案列为 Candidate，新问题列为关联议题，不藏在比较中。
- `Recommendation` 可省略，候选数量不触发推荐。出现时必须明确所属议题、目标候选或组合、判断标准及来源、关键取舍或成立条件；推荐保持 Candidate 地位。依据不足时保留 Open 或 Conditional，不强选。跨议题排序明确称为处理优先级，不作为互斥方案比较。
- Human-owned 未决选择使用 `[Open]`，并紧邻 `Human Decision Required` 说明目标、价值、scope、contract、risk 或 authority；Human Attention 仅简短引用，不重复展开。
- 每个本轮要求处理的问题都有明确回应；未覆盖部分说明原因及影响，不能通过不显示该议题伪装完成。

有界诊断、局部实现路径、可行性判断只作为对应 Candidate 的依据；排除应说明 evidence。“直接实现 A”等措辞按公共 Request Interpretation 披露为围绕 A 的候选请求，不执行实现。

以下为多议题示例，假设 Review 已给出 F1/F2/F3，且用户明确提出跨轮引用需求；推荐和比较仅因示例确有对应需要而出现：

```markdown
## Task Result

- Task: shape
- Status: complete
- Outcome: 问题归属形成两种替代提案；格式遗漏与规则误解分别有可组合的改善提案。
- Human Attention: none

### Context

#### Decision Space

##### Q1：如何保持问题与回答的对应？

Source: F1 — 多问题回答难以对应。

- [Candidate] Q1-A：按问题标题分组；标记少，但跨轮引用仍可能含混。
- [Candidate] Q1-B：标题加稳定编号；方便跨轮引用，代价是少量额外标记。

Comparison: Q1-A 与 Q1-B 是同一组织方式的替代方案。
Recommendation: 根据用户提出的跨轮引用需求，倾向 Q1-B，代价是增加少量标记。

##### Q2：如何减少输出格式偏差？

Source: F2 — 必填字段遗漏；F3 — 可选规则被误解。

- [Candidate] Q2-A：交付前检查必填字段；有助于发现遗漏，但依赖 Agent 执行。
- [Candidate] Q2-B：补充条件示例；有助于理解规则，但不能保证每次遵守。

Comparison: Q2-A 与 Q2-B 可组合，分别处理遗漏与理解偏差。
```

## Optional Context

- `Focus`：议题框架有重要修正或需要防止语义漂移时使用；说明当前解读及其与原目标的关系，保持可修正身份。Human Anchor 保留核心诉求；已在 Request Interpretation 的 Original 中呈现时简短引用，不重复展开。Current Take 是 Agent 的可修正模型；Alternative Read 只表达 materially different 解读。
- `Decision Criteria`：只有标准会真实区分候选时使用。简短说明标准来源；Agent 提出的 criteria 不自动成为 Human preference 或 Constraint。
- `Conditional`：只分析 premise 成立后的方向，不把 premise 当成事实。
- `Compatibility`：不创建专用 section。Material Surface 和 Boundary 使用 Fact、Constraint、Decision、Candidate 或 Open；重要 Boundary 附简短 Basis。Mechanism 只保持 Candidate；无 material Surface 时完全省略。
- `Reconciliation`：Human language、system meaning、Current Take、Constraint 或 Candidate 冲突并实质改变模型时使用公共结构；有价值的分歧可以 `preserved` 为议题内有依据的候选分歧或 Open Decision。
- `Possible Next Move`：只有方向本身提供新信息时使用，通常不超过 2 项；不是路由或授权。

## Multi-round Projection

连续 Shape 保留公共外壳，在 Context 的 `Model Delta` 下先按议题分组，再说明变化，并保留本轮必需的 Request Interpretation：

- `Added`：新增的 context、Candidate、criterion 或 Open；
- `Changed`：被 Human correction、evidence 或新 context 修正的内容；
- `Rejected`：已被 Human 或 evidence 排除的理解或候选。

议题与候选编号仅用于当前对话内引用，不是全局 ID 或持久状态。新议题追加编号；已有编号不重排、不改指其他内容。拆分或合并时为新议题分配新编号，说明旧新对应关系及候选映射，退役编号不复用。

续轮示例（用户明确接受 Q1-B，其他议题未变化）：

```markdown
## Task Result

- Task: shape
- Status: complete
- Outcome: 问题与回答的对应方式已明确为标题加稳定编号。
- Human Attention: none

### Context

#### Model Delta

##### Q1：如何保持问题与回答的对应？

- Changed: [Decision] 用户明确接受 Q1-B；Q1-A 不再作为当前选择。
```

用户理解后果后修正偏好时，用 Model Delta 同步相关 criteria、比较与推荐，不默认视为矛盾或要求重启。

短续轮不同时重建完整 Decision Space。Human 要求总结、context material conflict，或高风险执行前需要重新确认时才返回 consolidated view；Current Take 只有实质变化或需要 Human 纠正时重复。

## Human Attention

沿用公共定义，只保留最终未决事项；已解决的决定、仅需知悉的风险与可自主处理的暂定依据归对应 Context。

只放置最终必须由 Human 纠正、选择、授权或接受重要风险的事项。能通过 context 或局部检查消除的普通未知不成为 Human blocker。

Shape 不形成完整 Execution Model、正式 Change Surface、工作包、实施清单或现实修改授权；“尚未承诺”不是输出完整执行设计的例外。不输出完整对话、穷尽候选集或自动后续行动。
