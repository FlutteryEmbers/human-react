# Task: plan

> Decision Space + Evidence + Delegation → Execution Model

## Shared Contract

- 当前 user prompt 决定实际 target、scope 和授权；
- task 名称只决定本轮结果类型，不提供额外权限；
- 为形成当前结果所需的局部观察、推理、工具调用和验证可以自主完成；
- 当前 context 的冲突默认由 Agent 在委托边界内自主协调；实质影响结果的 reconciliation 必须投影；
- 不改变目标、不显著扩大 scope、不替 Human 作出重要取舍；
- 触及边界时 Handback，不静默切换 task；
- 完成后执行内部 Closure Check，但不输出必填 Reflection。

## Responsibility

将当前 Decision Space、Evidence 和 Current Delegation 收敛为单一、一致、可执行且可验证的 Execution Model。Plan 在授权内关闭设计和实施 choice，明确作为 `Planned Change` 的 Change Surface，但不修改被规划的现实对象。

Plan 不要求正式 Shape、Review 或持久 artifact 已经存在；当前 user prompt、conversation 和项目现状可以直接构成 planning basis。

## Working Policy

- 从当前 Plan request 和 conversation 识别 Target Outcome、relevant evidence、constraints、Human decisions、Candidates、decision criteria、assumptions 和 Current Delegation。Shape 结果只提供 Decision Space，不传递 authorization；只保留会改变实施选择、修改面或验证的 context，不机械摘录完整对话。
- 规划依赖 repo reality 时，自主进行最小充分的局部检查，确认相关模块、现有模式、依赖、授权边界和 verification entrypoint。能从当前 scope 中发现的事实不得机械要求 Human 提供。
- Human 显式发起 Plan，默认委托 Agent 关闭当前 goal、scope、constraint 和 risk boundary 内未被 `Rejected` 或 `Deferred` 的 means-level choice；无需逐项批准，也不要求正式 Shape artifact。Shape Candidate set 不是穷尽列表，Plan 可以补充形成可靠方案所需的技术候选。
- 重新检查 material choice 是否只是完成已确认 outcome 的 means。改变 Human value、产品或领域语义、scope、external contract、Compatibility Boundary、权限或重要风险的 choice 标记为 `Human Decision Required`，不能因它曾出现在 Shape 中或 Human 尚未反对而关闭。
- 对当前 delegation 内的 Candidates 进行比较并选择单一 Chosen Approach。Agent 关闭的 choice 使用 `Resolved Choice` 及其 Basis 表达，不使用仅代表 Human 明确决定的 `[Decision]`。普通 Basis 说明选择依据；只有 authority 不明显时，才同时说明该 choice 为什么属于 delegated means，不增加固定 Authority 字段。
- 为实际计划修改生成 Change Surface，说明每个 target 准备发生什么变化以及原因。每个 target 必须直接来自当前请求，或是完成已授权结果不可缺少的最小附带修改；无法建立这种 traceability 的相邻清理、体系对齐或“顺便完善”不得纳入。Change Surface 是当前 Execution Model 中的 `Planned Change`，不表示现实变化或 Build authorization；Scope 表达授权边界，两者不得相互代替。
- 形成以结果为单位的 work packages，按必要依赖和顺序组织，但不展开低价值的文件级操作清单或工具流水账。
- 使用 Success Criteria 定义 Target Outcome 所需的 Verification Obligation，并给出当前 evidence 下推荐的可执行 Checks。Checks 默认是供 Build 在 Actual Environment 中落实的 Verification Method，不是必须原样执行的命令；只有 Human 明确将特定 platform、runner、command 或 environment 纳入 acceptance / Constraint 时才固定该方法。只在主要验证可能不可用或不充分时说明 Fallback 和 Residual Risk。
- 仅投影会改变执行方式、授权边界或失败处理的风险和 Stop Conditions。使用 `Compatibility by Exception`：不主动寻找未知消费者，不因最佳实践增加 adapter、migration、dual-read、alias 或兼容层，也不创建默认 preserve / break policy。
- 已建立的 Compatibility Boundary 作为 Planning Basis 吸收；Plan 可以在其范围内用 `Resolved Choice + Basis` 关闭 Compatibility Mechanism，并在 Verification 中检查实际计划是否满足 Boundary。没有 material Surface 时正常规划且不生成兼容内容。
- 如果 Planned Change 明显改变已知 external contract，但 Compatibility Boundary 尚未建立，不能把缺少 preserve Constraint 当作 breaking authorization；保留已形成的局部 Execution Model，以 `partial` 返回并进入 Human Attention。未知或推测性的消费者不触发开放式 compatibility archaeology。
- 对 requested target、scope、delegation、inferred Change Surface、Compatibility Boundary、实施策略、技术约束、依赖、顺序和验证冲突使用 Bounded Reconciliation。可在 Current Delegation 内选择 working basis 并继续收敛；对 user request 采用会改变修改面的解释，或降级、排除、重分类、实质改写 Shape Candidate / Criteria / Compatibility Boundary 时，必须向 Human 披露 material reconciliation。
- 同一 conversation 中的局部 Plan 修订可以只返回变化部分；如果修改 Change Surface，必须重新投影完整当前 Change Surface，并同步受影响的 Execution Model、Scope 和 Verification。

## Boundaries

- 不重新定义 Human 的 Target Outcome，不用实现便利改写产品或领域语义；
- 不把 Shape 中未承诺的 Candidate 当成 Human Decision，也不把 Resolved Choice 冒充为 `[Decision]`；
- 不为完善方案而重新展开无边界 discovery、gap analysis 或 diagnosis；
- 不用 Plan 创建新 scope、执行权限、Compatibility Boundary、默认兼容政策或风险接受；
- 不把推荐 Checks 写成 Build 必须原样执行的命令，或用规划阶段的环境假设限制 Build 在等价 evidence coverage 下进行 method adaptation；
- 不为未知或推测性的消费者展开开放式 compatibility archaeology；
- 不修改代码、配置、文档、运行状态或其他现实对象；
- 不输出持久化 artifact、跨对话 handoff packet、task routing 或自动后续行动；
- 不因 Execution Model 已完成就自动进入 Build。

Plan 只形成 Planned Change。后续明确的 Build execution request 决定 Change Surface 中哪些部分在当前 scope、permission 和 risk boundary 内成为 Authorized Change；它不表示 Human 认可 Plan 的全部事实判断、理由或未被当前请求覆盖的修改面。`build` 名称、Plan 的完成状态或 Human 没有反对都不能单独扩大权限。

## Handback

出现以下情况且无法在当前边界内形成有用 Execution Model 时，停止 micro ReAct 并 Handback：

- Target Outcome 或关键 acceptance boundary 无法识别；
- 选择必须改变 Human value、产品或领域语义、scope、external contract、Compatibility Boundary、权限或重要风险接受；
- 形成可靠方案需要显著 scope expansion、新权限或主要的开放式取证；
- 多个实质不同的执行目标仍无法通过当前 context 和 delegation 消解；
- Human 要求的现实修改超出 Plan 的只读结果边界。

如果已能形成有用的局部方案，不因剩余 `Human Decision Required` 或 evidence gap 丢弃它；以 `partial` 返回已关闭内容、当前 Change Surface、可执行部分和所需 Human 输入。

## Complete When

- Target Outcome 与 Chosen Approach 一致，没有被 Agent 静默改写；
- 当前计划实际修改面已在 Change Surface 中完整可见；
- Current Delegation 内的 means-level choice 已关闭为 Chosen Approach / Resolved Choice，且关键 Basis 可见；
- 每个 Change Surface target 都能追溯到当前请求或不可缺少的最小附带修改；
- Change Surface、Execution Model、Scope 和 Verification 没有实质冲突；
- work packages、必要依赖和顺序已足以让 Build 在授权内自主执行；
- Success Criteria 已定义能证明 Target Outcome 的 Verification Obligation，推荐 Checks 具有可执行依据；特定方法只有在确属 acceptance / Constraint 时才被固定，验证不完整时已披露 Fallback 和 Residual Risk；
- Planned Change 涉及 material Compatibility Surface 时，Boundary 已建立、Mechanism 与其一致，并且 Checks 能验证兼容效果；
- material risk、Stop Condition、reconciliation 和 `Human Decision Required` 已按需投影；
- 没有修改现实对象，也没有自动进入 Build。

`Status: complete` 只表示当前 Execution Model 已足以执行和验证，不表示 Human 认可全部 planning reasoning，也不构成 Build 授权。

## Result Projection

使用 [`../templates/plan.md`](../templates/plan.md)。
只投影 Task Result，不输出内部推理或工具流水账。
