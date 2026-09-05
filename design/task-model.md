# Task Model Design

本文解释 Human ReAct 为什么采用 `orient / review / shape / plan / build` 五种结果型 task。具体运行规则由 [`.human-react/**`](../.human-react/) 定义。

## Why Result-oriented Tasks

传统 workflow 容易让 Human 根据最终目标选择 task，例如“最终要改代码”便直接选择 Build，但当前真正缺少的可能是解释、证据、语义模型或执行方案。Human ReAct 因此不把 task 当作项目阶段，而把它定义为本轮希望从 Agent 得到的结果。

这种划分让 Human 维持宏观 ReAct loop，同时把搜索、追踪、工具调用、局部 diagnosis 和验证留给 Agent 的 micro ReAct。Human 不需要调度 Agent 的内部认知步骤，只需判断当前最需要改变哪一种状态。

## Shared Design Dimensions

五个 task 通过同一组问题区分：

| Dimension | Design Question |
| --- | --- |
| Human need | Human 本轮需要理解、判断、建模、计划还是现实结果？ |
| State changed | 改变的是 Human understanding、evidence context、decision space、execution model 还是 reality？ |
| Direction of fit | 模型接受现实校正，还是意图经过有限委托作用于现实？ |
| Evidence boundary | 结论可以由哪些观察支持，哪些仍是解释或候选？ |
| Commitment | 本轮是否只讨论可能性，还是关闭实践选择？ |
| Control | 哪些选择仍由 Human 维持，哪些手段交给 Agent？ |
| Closure | 什么结果足以返回 Human，而不追求主题穷尽？ |

## Orient

Orient 对应“我需要先看懂什么”。它改变 Human 对 Subject 的 working understanding，而不判断 target 是否正确，也不形成设计承诺。

独立设置 Orient 的理由是：背景知识、机制解释和不同观察角度本身可以是有价值的终点。若全部塞进 Review，解释会被误读为 verdict；若塞进 Shape，知识会过早转化为 Candidate。

核心 tradeoff 是解释力与边界感。Orient 提供足够形成 mental model 的背景，但不追求百科式覆盖；它可以综合材料，却区分 target-specific fact、general model 和 interpretation。

刻意排除：correctness judgment、diagnosis、Candidate comparison、实施建议和业务现实修改。已知限制是 explanation 仍可能受 Agent framing 影响，因此其模型保持可修正，也不会自动进入项目 Memory。Human 显式选择 effectful Lens 时产生的固定 protocol sidecar 不改变 Orient 的解释责任。

## Review

Review 对应“现实现在哪，以及为什么”。它让 working model 接受 evidence 校正，形成 finding、gap、diagnosis、fitness judgment 或可靠的不确定性边界。

Review 独立于 Orient，因为理解机制不等于证明当前 target 的状态；也独立于 Build，因为观察和判断不应自动产生 target 修改权限。Diagnosis 属于 Review 的因果判断能力，而不是单独 task。Human 显式选择 effectful Lens 后创建的 protocol sidecar 是当前 prompt 的窄授权，不是 Review finding 自动产生的权限。

核心 tradeoff 是判断力与证据纪律。Classification 帮助 Human 快速看到 disposition，但不能替代 evidence；Conditional Analysis 允许沿未确认前提推演，但不改变 finding。

刻意排除：完整替代设计、实施计划和实际修复。已知限制包括 evidence quality、工具误差和隐藏条件仍可能使结论可错，因此 Review 接受 provisional closure。

## Shape

Shape 对应“我们究竟在讨论什么，以及有哪些尚未承诺的方向”。它改变 Decision Space，而不是 Reality。

Shape 独立存在，是因为 Human language、系统语义和实现机制通常不会天然对齐。Agent 在此扮演主动建模协作者：补充遗漏、反例、压力点和候选，但不冒充默认领域权威。

核心 tradeoff 是发散与可判断性。Shape 允许产生候选和 Alternative Read，却要求 Candidate 是可整体接受、拒绝或比较的 decision unit。多轮 Shape 用 Model Delta 吸收 Human 补充的 context，避免每轮重建全部讨论。

刻意排除：关闭所有实现选择、生成执行方案和传递现实授权。只有改变 Desired Effect、产品或领域语义、scope、external contract 或重要风险的例外需要 Human 明确决定。

## Plan

Plan 对应“基于当前现实，真正需要改什么以及怎样验证”。它把 Decision Space、evidence、delegation 和 Current State 收敛为 Required Delta 与必要的 Execution Model。

Plan 独立于 Shape，因为 Candidate 可以是前瞻性方向，不证明现实中已有 gap；也独立于 Build，因为计划修改面不是现实干预权限。Human 发起 Plan，使 Agent 可以在既有边界内关闭 means-level choice，而不需要逐项审批技术细节。

核心 tradeoff 是可执行性与过度规划。Change Surface 帮助 Human 一眼检查实际修改面；bounded impact inquiry 只追踪会改变修改面、验证、风险或 Human decision 的耦合，不构造完整 Effect Surface。若当前目标已经满足，Plan 可以以无需修改结束。

刻意排除：修改现实、默认兼容政策、未知消费者考古和相邻体系补全。已知限制是静态规划无法发现全部运行时耦合，因此 Build 仍需根据最新 Reality 复核。

## Build

Build 对应“让 Requested Outcome 在现实中成立，并用 evidence 判断结果”。它是唯一承担业务 target durable 或 material intervention 的 task，也是当前 delivery loop 的终点。Protocol-owned Lens sidecar 不属于业务 target intervention，也不扩大 Build request。

Build 不等于照单执行 Plan。Plan 提供 context，当前 Build request 才界定现实干预。Reality feedback 可以改变实施手段，但不会自行扩大 goal、scope、contract 或风险边界。

核心 tradeoff 是完成目标与限制扩散。Semantically Atomic Intervention 提供可观察的因果切片；Observable Boundary Gate 让 Agent 在稳定边界内继续、在不明时调查、在边界改变时返回 Human。这里追求的是恢复性和可审计性，不是隐藏耦合的形式化完备证明。

刻意排除：顺手修复独立问题、把验证失败转换成新权限、形成整体产品正确性 verdict，以及自动开启下一 loop。已知限制是同一 Agent 同时执行和自我监控仍可能漏判，因此结果声明受 Verification 和 Remaining Risk 约束。

## Cross-task Boundaries

```text
Orient changes Human understanding.
Review establishes evidence-backed judgment.
Shape constructs a Decision Space.
Plan forms a Required Delta and Execution Model when needed.
Build changes or confirms Reality and returns verified feedback.
```

这些边界避免几种常见混合：解释被当成事实判断、候选被当成 Human decision、计划被当成执行授权、sidecar authorization 被当成 target authorization、实际修改被当成目标已实现，以及 scope 允许被当成修改必要。

Task 之间可以跳过、重复或往返。一个清楚的小修改可以直接 Build；复杂问题可以在 Review 与 Shape 间多轮校准；Plan 也可以因新 evidence 回到 Human，而不是自动推进。

## Open Design Questions

当前仍保留的问题包括：

- episode capture 在什么实际规模下值得由 Human 显式 consolidation；
- declared Lens effect 是否需要从文档 contract 发展为 runtime enforcement；
- 如何用真实 harness eval 检查 task selection、projection compression 和 boundary detection；
- Prompt discipline 与未来可能的 runtime enforcement 应如何分工。

这些问题描述设计空间，不表示已有实现或默认后续路线。
