# Lenses Design Reservation

本目录记录 Human ReAct 对 Lens 的候选设计。它目前不是主系统的一部分，也不定义任何可运行行为。

## Candidate Model

Lens 是附加到当前 task 的可选 context module。它可以改变 Agent 关注什么、怎样检查证据以及使用哪些领域约束，但不得改变：

- task 的职责和结果类型；
- user prompt 的目标、scope 或授权；
- write、execute 或风险权限；
- task 之间由 Human 维持的宏观循环；
- templates 的公共输出契约。

候选体系区分三类 Lens：

| Type | Candidate Responsibility | Example |
| --- | --- | --- |
| Perspective Lens | 提供跨项目可复用的分析角度或证据标准 | architecture、boundary、debug、test、redteam |
| Posture Lens | 将 Project Profile 中持续的交付姿态解释为多个 task 的工作约束 | poc、production、migration-safe |
| Project Trace Lens | 提供项目特定系统的阶段模型、证据入口和诊断追踪协议 | Kotlin call-chain parsing、compiler lowering、billing settlement |

详细边界分别见 [`perspectives/`](perspectives/)、[`postures/`](postures/) 与 [`project/`](project/)。

“分离式分析”是一个候选 Perspective Lens 方向：它可以按 target 需要分开可观察内容、情绪信号、证据逻辑、条件延展与非证据猜想。Lens 只能强化关注维度，不得改写 Review 的证据纪律，也不得修改 task 职责、权限或公共输出契约。具体候选语义见 [`perspectives/`](perspectives/)。

## Relationship With Memory

Lens 不保存项目当前状态。[`memory/**`](../memory/) 中候选的 Project Profile 与 Topic Memory 负责提供状态和参数，Lens 负责解释这些 context 对 task 内行为意味着什么：

```text
Lens   = reusable behavior function
Memory = Human-governed project parameters
Task   = requested result and authority boundary
```

Perspective Lens 倾向于由 Human 当轮显式选择；Posture Lens 可以在未来由 Project Profile 声明持续 binding；Project Trace Lens 可以按 project scope 绑定。只有 Profile 中明确的 Human decision 才可能建立持续 binding，普通 Memory 内容不能自动激活 Lens。

## Project Trace Principle

Project Trace Lens 固定的是诊断坐标系，而不是结论：

```text
System Stages
+ Stage Input / Output Contracts
+ Evidence Sources
+ Invariants
+ First-divergence Rules
+ Verification Entry Points
= Repeatable Project-specific Investigation
```

它可以指导 Agent 拼接多层证据，例如从最终错误结果回溯到第一个发生偏差的阶段，再判断问题属于 upstream corruption、local violation 还是 downstream interpretation。

Lens 不应要求每次完整回放所有阶段。默认策略应是从 symptom 出发逐步扩大证据范围，只展开支持当前判断所需的阶段。

## Shared Boundaries

- Lens 是 task modifier，不是 task、workflow stage 或自动路由规则；
- Lens 可以提供检查方法，不能把 heuristic 当成 evidence；
- Project Lens 可以引用项目事实，但必须区分 source of truth、invariant、heuristic 和 unknown；
- Lens 不拥有独立输出模板；结果仍按当前 task 的 template 投影；
- Lens 不保存 Project Profile、session state、调查结果或 Human decision；
- task 输出的 reusable insight 不会自动写入或更新 Lens；
- Lens 不得用项目知识推断新的执行授权；
- Lens 内容与当前代码或规范冲突时，以重新观察到的项目证据为准，并暴露 Lens drift。

## Possible Future Activation

未来可以考虑：

- Perspective Lens 由 Human 显式选择；
- Posture Lens 由 Project Profile 中明确的 Human decision 绑定；
- Project Trace Lens 由 Human 指定，或由 Project Profile 声明 project scope binding；
- 默认最多一个 active Posture Lens 和一个主要 Project Trace Lens，Perspective Lens 只在明确需要时附加；
- 长 Lens package 先加载短入口，再按 symptom 读取相关 evidence module；
- 使用前先检查适用版本、关键路径和阶段是否仍然存在。

这些都只是候选规则。当前不存在 Lens 选择语法、自动匹配、加载顺序、组合优先级或运行时集成。

## Non-integration Status

当前必须保持：

- `tasks/**` 不引用 `lenses/**`；
- `templates/**` 不增加 Lens 字段；
- 没有正式 Lens 文件或默认 Lens；
- 没有 Project Profile binding、resolver、registry、schema、loader 或 adapter；
- Agent 不因本目录存在而自动应用任何 Lens。

本目录只有在后续设计通过真实 task 验证后，才能从 design reservation 进入主系统。
