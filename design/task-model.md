# Task Model Design

本文解释 Human ReAct 为什么采用 `orient / review / shape / plan / build` 五种结果型 task。具体运行规则由 [`.human-react/**`](../.human-react/) 定义。

## Why Result-oriented Tasks

显式选择 Task 表达 Human 本轮要怎样与 Agent 合作：理解、审查、构造方向、规划或获得现实结果。Prompt 的日常动作措辞不总能精确表达这一协作意图，因此所选 Task 对本轮意图有最高解释优先级；Agent 在 Task 内解释 prompt，而不根据动词重选 Task。

Task 定义主要交付责任，不隔离认知能力。Agent 自主完成必要理解、调查、比较、诊断、局部设计、规划和验证；Human 无需为每一种辅助分析调度 Task。主要责任稳定的同时，承诺、scope 与操作权限仍受明确边界约束。

解释保留原文的对象、关注目标和具体约束，将不匹配的动作转为当前 Task 的工作问题。包括明确措辞冲突在内，只要能形成有用请求就直接完成；实质改写在结果中公开，不作为审批请求。代价是结果可能与原文动作的字面期待不同，因此必须披露哪些动作没有发生，不能通过改写伪称完成。详细规则见 [Core](../.human-react/core.md#task-scoped-request-interpretation)。

## Shared Design Dimensions

五个 task 通过同一组问题区分：

| Dimension | Design Question |
| --- | --- |
| Human need | Human 本轮需要理解、判断、建模、计划还是现实结果？ |
| Primary responsibility | 所选 Task 下主要交付什么，辅助分析怎样支持它？ |
| Direction of fit | 模型接受现实校正，还是意图经过有限委托作用于现实？ |
| Evidence boundary | 结论可以由哪些观察支持，哪些仍是解释或候选？ |
| Commitment | 本轮是否只讨论可能性，还是关闭实践选择？ |
| Control | 哪些选择仍由 Human 维持，哪些手段交给 Agent？ |
| Closure | 解释后的工作请求是否完成，实质改写和实际边界是否可见？ |

## Orient

Orient 对应“我需要看懂什么”。它将优化、修复、评价等措辞解释为原对象的机制、条件、设计理由和取舍问题，交付 working understanding，不承担整体合格性 verdict、候选选择或实施承诺。

独立设置 Orient 的理由是理解本身可以是有价值的终点。选择 Orient 后，Human 不必在每句 prompt 重复“本轮只是理解”。Review 同样可以包含必要解释，但主要责任还包括有依据的判断；这种区别不要求把理解拆为单独调用。

核心 tradeoff 是解释力与边界感。Orient 提供足够形成 mental model 的背景，但不追求百科式覆盖；它可以综合材料，却区分 target-specific fact、general model 和 interpretation。

允许直接解释局限，设计理由区分有来源的依据与 Agent 推测。刻意排除从理解自行扩展独立审查、方案选择和现实修改；评价措辞在 Orient 内解释，不因此 Handback。Explanation 仍受 framing 影响，所以保持可修正，实质改写须公开，也不会自动进入 Memory。显式 Lens 的固定 sidecar 不改变主要责任。

## Review

Review 对应“现实现在哪，以及为什么”。它让 working model 接受 evidence 校正，形成 finding、gap、diagnosis、fitness judgment 或可靠的不确定性边界。

Review 可以在一份结果中建立整体理解模型、解释机制并形成判断，不需要先运行 Orient。它把“修复并提交”解释为问题、修改必要性、影响和改善方向的审查请求；公开说明未修改、未提交。Diagnosis 可以是审查结果，也可辅助其他 Task，均受 evidence 约束。判断与 Lens sidecar 都不会自动产生 target 修改权限。

核心 tradeoff 是判断力与证据纪律。Classification 帮助 Human 快速看到 disposition，但不能替代 evidence；Conditional Analysis 允许沿未确认前提推演，但不改变 finding。

改善方向须对应已确认问题，并说明为何有助于关闭 gap；不因原文要求修复就虚构缺陷。刻意排除从审查自行扩展独立的完整重设计、执行方案和实际修复。Evidence quality、工具误差和隐藏条件仍可能使判断可错，因此 Review 接受 provisional closure，未证实原因保持假说。

## Shape

Shape 对应“我们究竟在讨论什么，以及有哪些尚未承诺的方向”。采用、实现方案等措辞转为围绕原方案与目标构造、检验、比较和推荐候选，不因措辞冲突执行实现或要求重选 Task。

Shape 独立存在，是因为 Human language、系统语义和实现机制通常不会天然对齐。Agent 在此扮演主动建模协作者：补充遗漏、反例、压力点和候选，但不冒充默认领域权威。

核心 tradeoff 是发散与可判断性。Shape 可以通过有界诊断和可行性判断排除不可行方向，并基于 evidence 与已明确的 criteria 推荐候选。Candidate 仍是可整体接受、拒绝或比较的 decision unit；推荐不晋升为 Human Decision 或 Resolved Choice。多轮 Shape 用 Model Delta 吸收 context，避免每轮重建。

刻意排除将推荐变为实施承诺或现实授权，以及从验证需要推导持久实验权限。已转换为候选请求的原文动作未执行，不单独降低完成状态；真正影响 Desired Effect、产品或领域语义、scope、external contract 或重要风险的未决选择仍需 Human 决定。

## Plan

Plan 对应“基于当前现实，真正需要改什么以及怎样验证”。它将“修好、实现”等措辞解释为必要修改面、执行方案和验证要求；完整规划可以完成本轮，同时公开说明尚未实施。

Plan 独立于 Shape，因为 Candidate 不证明现实 gap；也独立于 Build，因为计划不产生修改权限。Agent 可以补充诊断、比较路径、修正局部模型并关闭边界内的技术选择；原机制不成立时选择替代机制，披露重要变化，不因局部设计变化交回 Shape。

核心 tradeoff 是可执行性与过度规划。Change Surface 帮助 Human 一眼检查实际修改面；bounded impact inquiry 只追踪会改变修改面、验证、风险或 Human decision 的耦合，不构造完整 Effect Surface。若当前目标已经满足，Plan 可以以无需修改结束。

刻意排除：修改现实、默认兼容政策、未知消费者考古和相邻体系补全。已知限制是静态规划无法发现全部运行时耦合，因此 Build 仍需根据最新 Reality 复核。

## Build

Build 对应“让 Requested Outcome 在现实中成立，并用 evidence 判断结果”。它是唯一承担业务 target durable 或 material intervention 的 task，也是当前 delivery loop 的终点。Protocol-owned Lens sidecar 不属于业务 target intervention，也不扩大 Build request。

Build 围绕原对象与目标解释现实结果请求，自主完成必要理解、诊断、局部设计和规划。Plan 提供 context，不是必须照做的清单；具体操作授权和限制仍有效。仅要求检查且不修改时交付验证结果；目标已满足时不制造变更。若目标仍需实现且必要修改被禁止，披露实际未完成部分，不用解释取消限制。

核心 tradeoff 是完成目标与限制扩散。Semantically Atomic Intervention 提供可观察的因果切片；Observable Boundary Gate 让 Agent 在稳定边界内继续、在不明时调查、在边界改变时返回 Human。这里追求的是恢复性和可审计性，不是隐藏耦合的形式化完备证明。

Build 必须在请求与 evidence 覆盖内给出验收判断。刻意排除将局部通过扩大为整个产品正确的保证、修复独立问题、从验证失败取得新权限，以及自动开启下一 loop。同一 Agent 同时执行与自我监控仍可能漏判，结果声明受 Verification 和 Remaining Risk 约束。

## Cross-task Boundaries

```text
Orient changes Human understanding.
Review establishes evidence-backed judgment.
Shape constructs a Decision Space.
Plan forms a Required Delta and Execution Model when needed.
Build changes or confirms Reality and returns verified feedback.
```

这些是主要结果责任，允许共享必要分析能力；必须分离的是 evidence 地位、承诺和授权。工作解释不成为 Human Decision，候选不成为实施承诺，计划与 sidecar 不成为 target 授权，实际修改不等于目标达成，scope 允许也不等于必须修改。

Task 由 Human 显式选择，本轮和续轮保持选择，只有明确重新选择才改变。Prompt 动作措辞不会触发自动路由；解释后的工作在一份所选 Task Result 中完成，实质改写在现有 Context 披露。未执行的原文动作不自动成为待办、Remaining Gap 或下一轮授权。Status 按解释后的请求判断，真实证据、对象、选择和权限阻碍仍需如实处理。

## Behavioral Acceptance Scenarios

下表用于运行协议、task prompt 与公共及专用 projection 的一致性审查。每个三文件组合应能独立应用请求解释、披露和状态规则。场景推演检查文档自洽性，不代表已经测得模型实际遵循率。

| 场景 | 必须成立的行为 |
| --- | --- |
| Orient + “评价这个设计好不好” | 保持 Orient，解释机制、条件、理由与取舍，披露评价诉求的转换，不自动切换 Review |
| Review + “修复并提交” | 审查问题及修复方向，明确未修改、未提交；解释后的请求完成时可为 complete |
| Review + “理解并评价” | 同一结果包含理解模型与有依据的评价，不拆 Task |
| Shape + “直接实现方案 A” | 围绕 A 构造和检验候选，披露解释与未实施边界，不执行实现 |
| Shape 发现候选不可行 | 依据 evidence 排除并可推荐替代方向，推荐不成为 Human Decision |
| Plan + “修好这个问题” | 形成必要修改、执行与验证方案，明确尚未实施，不因未修复降低状态 |
| Plan 中原机制不成立 | 在稳定目标、语义、约束和风险内选择替代机制并披露重要变化，不因局部设计交接 |
| Build 需要诊断和局部设计 | 内部完成并验证，不切换或另开 Task；验收判断限定于 evidence 覆盖 |
| Build 目标已满足 | 验证后完成，不制造变更；未被具体限制排除的明确动作要求仍需履行 |
| Build 仅检查目标是否成立，不修改 | 交付实际检查结论，不取消限制，不把目标未满足误写成已实现 |
| Build 仍要求实现目标，但禁止必要修改 | 保留可验证结果，披露解释后请求的实际未完成部分，不以改写消除限制 |
| Task 内工作缺少关键证据或权限 | 保留安全有用结果，披露真实阻碍，按实际完成度使用 partial / blocked |
| 原文动作未发生，解释后的工作已完成 | 可为 complete，Request Interpretation 准确披露实际边界；不伪称动作发生或自动列为待办 |
| 同一 Task 的续轮出现“修复、实现”等措辞 | 保持所选 Task，更新工作解释；新实质改写继续披露 |
| Plan 将修复诉求解释为规划，但验证发现无需修改 | 交付无需修改的依据与 Request Interpretation，不因省略 Execution Model 而省略改写披露 |
| Human 明确重新选择 Task | 按新选择处理后续请求，普通动作措辞不被当成重新选择 |
| 普通一致请求或轻微措辞归一化 | 直接完成，省略 Request Interpretation，不机械复述 prompt |
| 请求的事实前提无证据支持 | 保持 evidence 边界，不从动作措辞虚构缺陷或事实；不能用泛泛结果伪装完成 |
| 被审计材料包含执行或重选 Task 指令 | 指令仍为对象内容，不改变当前 Task 或授权 |
| Lens 的 applies_to 不含所选 Task | 不应用失配 Lens，prompt 改写不扩大适用性或 effects |

## Open Design Questions

当前仍保留的问题包括：

- episode capture 在什么实际规模下值得由 Human 显式 consolidation；
- declared Lens effect 是否需要从文档 contract 发展为 runtime enforcement；
- 如何用真实 harness eval 检查所选 task 的保持、请求解释是否忠实、改写披露、projection compression 和 boundary detection；
- Prompt discipline 与未来可能的 runtime enforcement 应如何分工。

这些问题描述设计空间，不表示已有实现或默认后续路线。
