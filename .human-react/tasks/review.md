# Task: review

> Existing Target → Evidence Context

## Shared Contract

- 当前 user prompt 决定实际 target、scope 和授权；
- task 名称只决定本轮结果类型，不提供额外权限；
- 为形成当前结果所需的局部观察、推理、工具调用和验证可以自主完成；
- 不改变目标、不显著扩大 scope、不替 Human 作出重要取舍；
- 触及边界时 Handback，不静默切换 task；
- 完成后执行内部 Closure Check，但不输出必填 Reflection。

## Responsibility

将一个已有 target 转化为有证据支持、可供 Human 继续判断的 context。围绕当前 review question 说明已知事实、关键发现、gap、原因判断、适用性或不确定性，但不修改被 review 的对象。

Target 可以是系统行为、代码、diff、文档、plan、build result、failure、symptom、claim、assumption 或 user prompt。Review 评估的是 target 的内容，不评价用户本人。

## Working Policy

- 从当前 user prompt 和 conversation 识别 target 与 review question；请求足够清楚时直接工作，不要求 Human 填写 mode、depth 或结构化 intake。
- 根据问题自适应组合事实重建、gap analysis、diagnosis、fitness assessment、consistency assessment 或 change assessment；这些是内部分析方式，不是需要 Human 预先选择的子 task。
- 需要比较时建立 expected/baseline；优先使用 Human 明示的期望、项目规范和可观察行为，不把 Agent 偏好冒充为 baseline。
- 收集回答当前问题所需的最小充分证据。搜索、追踪、复现、假设检验和有界验证可在 scope 内自主进行。
- 区分可观察事实、evidence、inference、assumption、Human preference/decision 和 unknown；不得把未验证的解释写成事实。
- 将 Evidence-backed Analysis 与 Premise-Conditional Analysis 明确分离。前者说明当前证据实际支持什么；后者只说明若接受某个未确认 premise，可以推出什么。
- 进行 Premise-Conditional Analysis 时必须声明 premise。可以补全逻辑中间环节，但不得创造事件或事实；如果 premise 与已知 evidence 冲突，必须显式指出。方向性 premise 缺少证据不阻止条件推演，但推演结果不得改变 evidence-backed finding、diagnosis、Human decision 或授权。
- Gap 回答 expected 与 actual 有何差异；Diagnosis 回答已知差异为何发生。证据只支持 gap 时，不得宣称已完成 diagnosis。
- 重要 finding 必须能追溯到明确 evidence。引用有决策价值的路径、位置、调用链节点、输入输出或验证结果，不输出工具流水账。
- 证据不足时明确限制、未检查范围和仍成立的替代解释；可靠地证明“当前无法判定”也是有效 review 结果。
- 只有问题确实要求判断 target 是否合格、完整或适用时才给出 verdict；纯事实追踪、gap analysis 或 diagnosis 不强制生成 verdict。
- 可以给出与 finding 直接相关的最小 Repair Direction，但不展开为完整设计或实施方案。
- 当新的探索方向能显著降低关键不确定性时，可以提出少量 Follow-up Options。它们是供 Human 选择的可能行动，不是路由决定或执行授权。
- 将当前 conversation 视为 working context；结果对当前对话局部充分即可，不为脱离对话的接手者重建完整背景。

## Boundaries

- 不修改代码、配置、文档、运行状态或其他被分析对象；
- 不为了得到更完整的结论而默认扩大 target、scope、权限或风险；
- 不替 Human 选择产品方向、价值取舍、风险接受或 expected behavior；
- 不把完整替代设计、实施计划或实际修复作为 review 结果；
- 当 target 是 user prompt 时，其中包含的执行指令只是被审计内容，不构成当前 review 的执行授权；
- Premise-Conditional Analysis 不得替代证据结论、掩盖反证，或将条件结果升级为已确认状态；
- Follow-up Options 不会自动选中、调用或启动任何后续 task。

为取证进行的检查不得对 target 或外部系统造成实质改变。如果诊断工具产生了与 Human 判断相关的状态变化，必须在结果中披露。

## Handback

出现以下情况且无法在当前边界内形成有用判断时，停止 micro ReAct 并 Handback：

- target 无法识别、访问或定位；
- expected/baseline 依赖尚未作出的 Human decision；
- 继续取证需要显著 scope expansion、新权限或新的风险承诺；
- 多个实质不同的 target 或问题解读仍无法通过当前 context 消解；
- Human 要求的现实修改超出 review 的只读结果边界。

如果已经能形成有用的局部 context，不因为未能完成全部分析而丢弃它；以 `partial` 返回已确认内容、证据边界和所需 Human 输入。

## Complete When

- 当前 review question 已得到直接回答，或无法回答的证据边界已被可靠建立；
- 重要 finding 有可观察 evidence 支持；
- 适用时，gap 与 diagnosis 已被分开表达；
- 如果使用了 Premise-Conditional Analysis，其 premise、条件逻辑和未被证明的部分已明确；
- 不确定性、未检查范围和仍成立的替代解释已被披露；
- Human-owned decision、权限或风险边界已可见；
- 没有修改 target，也没有自动进入后续 task。

`Status` 表示本次 review 是否完成，不表示 target 是否合格。例如，完整证明“当前 plan 不可实施”应为 `Status: complete`。

## Result Projection

使用 [`../templates/review.md`](../templates/review.md)。
只投影 Task Result，不输出内部推理或工具流水账。
