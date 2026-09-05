# Shape Result Projection

Shape 返回可修正的 Modeling Checkpoint。使用 [`common.md`](common.md)；Outcome 只说明本轮最重要的语义或 Decision Space 变化，不使用过程描述。

## Status

- `complete`：本轮需要的 working model 或 Decision Space 已形成；
- `partial`：已有有用模型，但关键歧义或 Human-owned decision 仍限制结果；
- `blocked`：无法识别 Human Anchor 或必要边界，且不能形成任何有用模型。

Status 按 Shape 下解释后的候选请求判断，实质改写遵循公共 Request Interpretation；原文实现动作未执行不单独降低状态。Status 不表示候选已收敛、Plan ready 或 Build 已授权。

## Default Projection

首次或 consolidated Shape 默认使用：

```markdown
## Task Result

- Task: shape
- Status: complete | partial | blocked
- Outcome: <本轮建立或修正的模型>
- Human Attention: none

### Context

#### Decision Space

- [Fact | Constraint | Decision] <已建立的状态>
- [Candidate] <可整体接受、拒绝或比较的 proposal>
- Pressure Point: <真正改变选择的取舍；适用时>
- [Open] <需要 Human 决定时>
  - Human Decision Required: <涉及的目标、价值、scope、contract、risk 或 authority>
```

不要求列出所有类型。`[Candidate]` 是最小 decision unit；需要共同接受的组成部分合并，只有可独立决定或互斥时才拆分。`[Deferred]` 表示当前明确不吸收的内容，不等于 Rejected。

有界诊断、局部实现路径、可行性判断与比较只能作为对应 Candidate 的可行性、成本和约束依据进入 Decision Space；排除应说明 evidence，推荐应说明依据、criteria 与取舍，保持候选地位，不标为 Human Decision 或 Resolved Choice。“直接实现 A”等措辞按公共 Request Interpretation 披露为围绕 A 的候选请求，不执行实现。

## Optional Context

- `Focus`：只在需要防止语义漂移时使用。Human Anchor 保留核心诉求；已在 Request Interpretation 的 Original 中呈现时简短引用，不重复展开。Current Take 是 Agent 的可修正模型；Alternative Read 只表达 materially different 解读。
- `Decision Criteria`：只有标准会真实区分候选时使用。Agent 提出的 criteria 不自动成为 Human preference 或 Constraint。
- `Conditional`：只分析 premise 成立后的方向，不把 premise 当成事实。
- `Compatibility`：不创建专用 section。Material Surface 和 Boundary 使用 Fact、Constraint、Decision、Candidate 或 Open；重要 Boundary 附简短 Basis。Mechanism 只保持 Candidate；无 material Surface 时完全省略。
- `Reconciliation`：Human language、system meaning、Current Take、Constraint 或 Candidate 冲突并实质改变模型时使用公共结构；有价值的分歧可以 `preserved` 为 Pressure Point 或 Open Decision。
- `Possible Next Move`：只有方向本身提供新信息时使用，通常不超过 2 项；不是路由或授权。

## Multi-round Projection

连续 Shape 默认在 Context 中输出 `Model Delta`，并保留本轮必需的 Request Interpretation：

- `Added`：新增的 context、Candidate、criterion 或 Open；
- `Changed`：被 Human correction、evidence 或新 context 修正的内容；
- `Rejected`：已被 Human 或 evidence 排除的理解或候选。

短续轮不同时重建完整 Decision Space。Human 要求总结、context material conflict，或高风险执行前需要重新确认时才返回 consolidated view；Current Take 只有实质变化或需要 Human 纠正时重复。

## Human Attention

只放置必须由 Human 纠正、选择、授权或承担风险的事项。能通过 context 或局部检查消除的普通未知不成为 Human blocker。

Shape 不形成完整 Execution Model、正式 Change Surface、工作包、实施清单或现实修改授权；“尚未承诺”不是输出完整执行设计的例外。不输出完整对话、穷尽候选集或自动后续行动。
