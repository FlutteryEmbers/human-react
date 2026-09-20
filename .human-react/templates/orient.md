# Orient Result Projection

Orient 返回面向 Human understanding 的 Scoped Explanatory Model。使用 [`common.md`](common.md)；Outcome 直接回答 Learning Question，不以过程描述代替解释。

## Status

- `complete`：已形成最小充分、有边界的解释，且显式 Lens 的 declared effect 已按需完成；
- `partial`：已有有用解释，但 material context、evidence、Human meaning 或 declared effect 仍不完整；
- `blocked`：无法识别 Subject / Learning Question，且不能形成任何有用解释。

Status 按 Orient 解释后的理解请求及公共 Request Interpretation 规则判断；原文评价或修复动作被转为理解对象，不单独降低状态。`complete` 依据解释交付，不表示已验证 Human 掌握，不要求理解确认，也不表示 Subject 已被穷尽或解释不可修正。仅澄清问题不算完成。

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

如果 Outcome 已足以回答简单问题且没有实质改写，可以省略 Context。Understanding Model 可包含设计理由、适用条件、取舍和局限，区分有来源的理由与 Agent 推测；按理解顺序组织，不按检索顺序，也不把详细等同于穷尽。

## Optional Context

- `Learning Focus`：Subject、Question 或 Intended Understanding 容易漂移，或焦点经过实质校准且影响结果理解时使用。简短说明当前问题与解释范围，区分 Human 明确需求与 Agent 暂定假设；不重复明确请求，不输出问答流水账、分类评分或用户能力标签。
- `Relevant Background`：只保留直接改善当前解释的知识，并简短说明 relevance。
- `Alternative Perspectives`：只有 materially different 视角能改变 Human 看见的结构或边界时使用，通常不超过 2 项；说明它揭示什么以及不能据此建立什么。
- `Understanding Boundary`：target-specific Fact、General Model、Interpretation 和 Unknown 可能被混淆时使用，空项省略。
- `Reconciliation`：通用模型、Human assertion、documentation 与 target evidence 的冲突实质改变解释时使用公共结构；routine terminology normalization 省略。

同一 conversation 中的 follow-up 默认只补充或修正被追问部分。Human 要求总结、旧解释 material conflict，或局部回答会造成整体误解时才重建 consolidated model。

## Required Content And Compression

首次及续轮均保留公共外壳。Understanding Model 只在 Outcome 不足以解释时展开；省略 Context 必须同时满足没有任何公共条件必需内容。实质改写、影响判断的 reconciliation 或 declared effect receipt 不因短回答省略。

## Human Attention

沿用公共定义，只保留最终未决事项；已解决的决定、仅需知悉的风险与可自主处理的暂定依据归对应 Context。

只放置 Human 必须明确的 Subject、meaning、intended use 或 materially different learning direction。能由当前 context 或只读检查消除的未知不成为 Human blocker。Human 说不清困惑或未反馈本身不成为 blocker；有依据的焦点假设按需放入 Learning Focus，不机械转为 Human Attention。

Orient 交付理解模型和必要的改写披露；原文中的评价、优化或修复诉求转为相关机制、条件和取舍的说明，不自行形成整体 verdict、候选 Decision Space、Execution Model、Change Surface、现实修改建议包或实施承诺。Reading list、检索流水账、持久化建议与自动 next-task 不进入结果。
