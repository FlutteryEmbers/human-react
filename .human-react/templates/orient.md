# Orient Result Projection

Orient 返回面向 Human understanding 的 Scoped Explanatory Model。使用 [`common.md`](common.md)；Outcome 直接回答 Learning Question，不以过程描述代替解释。

## Status

- `complete`：已形成最小充分、有边界的解释，且显式 Lens 的 declared effect 已按需完成；
- `partial`：已有有用解释，但 material context、evidence、Human meaning 或 declared effect 仍不完整；
- `blocked`：无法识别 Subject / Learning Question，且不能形成任何有用解释。

`complete` 不表示 Subject 已被穷尽或解释不可修正。

## Default Projection

普通 Orient 只使用：

```markdown
## Task Result

- Task: orient
- Status: complete | partial | blocked
- Outcome: <对 Learning Question 的直接回答>
- Human Attention: none

### Context

#### Understanding Model

<形成理解所需的结构、机制或关系>
```

如果 Outcome 已足以回答简单问题，可以省略 Context。Understanding Model 按理解顺序组织，不按检索顺序，也不把详细等同于穷尽。

## Optional Context

- `Learning Focus`：Subject、Question 或 Intended Understanding 容易漂移时使用，不重复明确请求。
- `Relevant Background`：只保留直接改善当前解释的知识，并简短说明 relevance。
- `Alternative Perspectives`：只有 materially different 视角能改变 Human 看见的结构或边界时使用，通常不超过 2 项；说明它揭示什么以及不能据此建立什么。
- `Understanding Boundary`：target-specific Fact、General Model、Interpretation 和 Unknown 可能被混淆时使用，空项省略。
- `Reconciliation`：通用模型、Human assertion、documentation 与 target evidence 的冲突实质改变解释时使用公共结构；routine terminology normalization 省略。

同一 conversation 中的 follow-up 默认只补充或修正被追问部分。Human 要求总结、旧解释 material conflict，或局部回答会造成整体误解时才重建 consolidated model。

## Human Attention

只放置 Human 必须明确的 Subject、meaning、intended use 或 materially different learning direction。能由当前 context 或只读检查消除的未知不成为 Human blocker。

Orient 不输出 verdict、Candidate、实施建议、reading list、检索流水账、持久化建议或自动 next-task。
