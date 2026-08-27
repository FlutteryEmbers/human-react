# Human ReAct Workspace

本目录是 Human ReAct 的可嵌入 Prompt Workspace。

Human 维持宏观目标、上下文和授权边界，并通过 user prompt 选择本轮委托模式。Agent 在该委托边界内自主运行局部 ReAct loop；完成或触及边界后返回 Human，由 Human 决定下一轮如何继续。

常见实践是由 Human 维持的嵌套反馈系统：review 将现实、产物和当前判断转化为可判断的 evidence context；shape 在多轮 Human context 中收敛 intent；plan 在多轮 execution context 中收敛实施方案；build 改变现实。详见[实际循环说明](loop.md)。

## Tasks

Task 不是必须依次执行的 workflow stage，也不是 Agent 内部推理步骤。它描述 Human 本轮希望获得的结果类型：

- [`review`](tasks/review.md)：将已有 target 转化为 evidence context；
- [`shape`](tasks/shape.md)：将 Human context 和显式决策收敛成有边界的 intent model；
- [`plan`](tasks/plan.md)：吸收相关 context、解决实施层冲突，形成可执行且可验证的 execution model；
- [`build`](tasks/build.md)：在授权范围内将 intent 或 plan 转化为现实并验证。

选择一个 task 不代表其他 task 已经完成，也不建立前置依赖。Agent 可以在当前 task 内完成为本轮结果所必需的局部观察、推理、规划、工具调用和验证。

## 最小规则

1. Human 负责宏观目标、授权边界、结果解释和下一轮委托；
2. task 表示本轮委托模式，不表示固定流程位置；
3. Agent 的自治覆盖当前委托所需的局部 ReAct loop；
4. task 名称、建议和前序结果本身不构成执行授权；
5. task 之间不存在自动转换，Human 可以跳过、重复或直接选择任意 task；
6. 需要改变目标、显著扩大 scope、取得新权限或作出重要取舍时，Agent 必须 Handback；
7. task 完成后，Agent 返回结果和边界，不自动开始下一 task。

`review ↔ shape`、`plan ↔ review` 和 `build ↔ review` 是常见的人类操作模式，不是强制顺序或自动路由。Human 根据 review 暴露的问题层级决定下一轮委托。

`Human Context ↔ shape` 和 `Human Execution Context ↔ plan` 是同-task 多轮收敛循环。每一轮仍由 Human 发起；Agent 更新当前 intent 或 execution model 后 Handback，不自动要求或启动下一轮。

## Review

Review 是统一的 Observation 入口。它可以分析现实状态、行为、代码或 diff、shape、plan、build result，也可以审计 user prompt 中的事实主张、原因假设、目标、约束和执行请求。它只评价 prompt 的内容，不评价用户本人，也不替 Human 作出关键决定。

Review 可以在内部进行搜索与事实重建、gap analysis、diagnosis 和 fitness assessment。这些是 Agent 为形成 evidence-backed context 所使用的分析动作，不是需要 Human 分别选择的顶层 task。Diagnosis 与 gap analysis 仍是不同概念，但可以被同一个 review 委托吸收；无论是否定位到原因，review 都不得自动修复或进入下一 task。详见 [Review、Gap Analysis 与 Diagnosis Note](review-and-diagnosis.md)。

`explore` 不再是正式 task，而是所有 task 都可使用的内部能力。四个 task 文件当前故意保持为空。目录现阶段只锁定结构与命名；后续 prompt 应定义每个 task 的预期结果、允许的内部操作、外部效果边界和 Handback 条件，而不是规定固定执行步骤。

四个 task 的详细定位见 [Task 定位](task-model.md)。

## Templates

[`templates/**`](templates/) 定义四个 task 的默认 chat projection。Template 不保存实际输出或运行状态，也不改变 task 的职责和授权边界；具体说明见 [Templates README](templates/README.md)。
