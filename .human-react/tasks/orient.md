# Task: orient

> Subject + Learning Need + Available Context → Scoped Explanatory Model

## Shared Contract

- 当前 user prompt 决定 target、scope 和权限；task 名称不增加授权，Human 显式选择 effectful Lens 时只授权其 declared sidecar；
- 为形成当前结果所需的局部观察、推理和工具调用可以自主完成；
- material reconciliation 必须可见；不改变目标、不显著扩大 scope、不替 Human 作重要取舍；
- 触及边界时 Handback，完成后不自动进入下一 task。

## Responsibility

围绕当前 Subject 和 Learning Need，为 Human 形成有边界、可修正、足以继续理解和提问的 Scoped Explanatory Model。Orient 可以解释术语、结构、机制、关系、必要背景和少量不同视角，但不形成评价、决策、计划或现实修改。

## Working Policy

- 从 prompt 和 conversation 识别 Subject、Learning Question 与必要的 Intended Understanding；请求明确时直接回答，只有不同解读会实质改变说明时才暴露歧义。
- 先回答 Learning Question，再按理解需要组织概念、组成部分、关系、因果或过程。达到最小充分解释后停止，不以展示检索量或穷尽主题为目标。
- 区分 target-specific Fact、General Model、Agent Interpretation、Perspective、Assumption、Conditional 和 Unknown；通用知识不自动成为项目事实。
- Relevant Background 必须说明它怎样改善当前理解。Alternative Perspective 只在它揭示 materially different 的结构或边界时提供，并说明不能据此建立什么。
- 可以在只读边界内搜索、阅读、追踪和观察。General model、Human assertion、documentation 与 target evidence 冲突时进行 Bounded Reconciliation，不用通用模型覆盖项目现实。
- Human 显式选择适用于 Orient 的 effectful Lens 时，按 Lens metadata 执行固定 protocol-owned sidecar；该 effect 不改变 Subject、Learning Question、Orient Result 或 target 的只读边界。
- 多轮 Orient 默认只补充或修正被追问部分；Human 要求总结、旧解释发生 material conflict，或局部回答会造成整体误解时才重建 consolidated model。

## Boundaries

- 不形成 correctness、fitness、gap、diagnosis 或 Review verdict；
- 不产生 Candidate、Decision Space、Resolved Choice、Change Surface 或 Build authorization；
- 不修改代码、被解释文档、运行状态或外部系统；显式 Lens 声明的固定 sidecar 是唯一持久化例外；
- 不生成百科式 background packet、reading list、检索流水账、未声明的持久化内容或自动后续行动。

## Handback

Subject 或 Learning Question 的不同解读会实质改变结果且无法消解，继续需要 Human 选择 meaning、intended use、产品语义或 scope，或请求核心已变成判断、计划或现实修改时 Handback。已有有用解释时返回 `partial`；只有无法形成任何有用解释时才 `blocked`。

## Complete When

- Outcome 直接回答 Learning Question，并形成 Human 可用的最小充分模型；
- Fact、General Model、Interpretation、Perspective 和 Unknown 没有混合；
- material conflict 与理解边界已按需可见；
- 没有形成其他 task 的结果或未声明的持久副作用；declared Lens sidecar 已完成并披露，控制权已返回 Human。

`complete` 不表示 Subject 已被穷尽或解释不可修正。

## Result Projection

使用 [`../templates/orient.md`](../templates/orient.md)，只投影结果，不输出检索过程或隐藏推理。
