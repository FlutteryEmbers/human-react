# Memory Model

本文记录 Human ReAct 采用 episode-based Memory Capture 的设计理由。它不是运行规范；正式加载、捕获、条目门槛和治理 contract 以 [`.human-react/memory/README.md`](../.human-react/memory/README.md) 为准。

## Memory As Additional Context

Memory 回答“关于这个项目，过去已经观察、确认或验证过什么”，而不是“当前 task 应产生什么结果”或“Agent 应怎样行动”。它是 Human-governed、project-specific additional context：

```text
Task Contract
+ Current Prompt
+ Human-loaded Memory
+ Human-selected Lens
→ Project-specialized micro ReAct
```

Task 仍拥有结果责任，Lens 提供 attention 与方法修饰，Tool 提供 capability。Memory 不成为 source of truth、task router、Lens activation、Build authorization 或隐藏控制器。Human 手动加载只允许它进入 context，不把其中内容自动晋升为 Fact、Decision 或 Constraint。

## Why Episode Documents

v1 每次显式使用 `memory-capture` 创建一个新的 Task episode 文档，文档内可以有多条合格记录，但不增量维护历史文档。选择这一结构是为了优先验证可追踪性和治理成本，而不是提前优化长期规模：

| Episode Capture | Consolidated Topic Memory |
| --- | --- |
| 每次形成新文档，来源直接对应当前 task | 需要读取、合并和重写历史内容 |
| Human 可以删除整个 episode | Human 必须理解并编辑局部 current view |
| 矛盾和重复保持可见 | 总结可能压平分歧或来源 |
| Agent 只判断当前内容是否通过 Capture Gate | Agent 还要承担去重、权威选择和 compact |
| 初期简单，长期可能文件增多 | 初期复杂，规模化读取更紧凑 |

因此 v1 允许 corpus 逐个文档增长，但一个 capture 在 Handback 后不再由 Agent 更新。这里的“非增量”指不修改、合并或总结过去的 Memory，不表示系统只能保存一条知识。

## Capture Is Not Promotion

自动创建文档和 Human 未来是否相信内容是两件事：

```text
Captured ≠ accepted as truth
Retained ≠ Human-confirmed
Loaded ≠ authorized to act
```

为了降低污染，v1 不保存开放式 Candidate、Assumption 或未验证 workaround，只接受两类高门槛内容：

- Operational Friction 必须包含真实失败、已执行 Resolution、成功 Verification 和复用边界；
- Project Bearing 必须有项目 evidence 或 Human confirmation、明确 Scope 与失效条件。

Human 删除是主要 negative feedback，但没有删除不产生 silent confirmation。加载时仍要根据当前 Reality 校验 Source、Scope、Basis 和 Invalid When。

## Why One Document Per Invocation

一个文档代表一次 Task episode，而不是单一 claim。同一 invocation 中分别满足门槛的多个 friction 或 bearing 可以写入同一文档，这使 capture effect 保持为一次、来源保持集中，也避免为每项观察生成文件。

Lens activation 时立即创建文档，即使最终没有合格条目也保留最小 skeleton。这样 Human 能观察每次 effectful Lens 使用及其结果，并通过删除决定是否保留；系统不自动清理空文档。

## Effect And Task Boundary

Memory Capture 是 Human 通过显式选择 effectful Lens 授权的 protocol-owned sidecar。授权范围在 Lens metadata 中预先声明，因此它不会从 Task completion、Reusable Insight 或工具可用性中推导出来。

Orient 和 Review 仍不能修改业务 target；Build 仍独占业务现实的 durable intervention。Capture sidecar 只允许在固定目录创建一个新文档，并在当前 invocation 内填充。现有 Memory 对 Agent 只读，effect failure 也不能触发替代路径、能力安装或权限扩张。

## Manual Loading And Maintenance

未来 task 只有在 Human 点名具体文件并要求使用时才加载 capture。系统不根据目录、scope、内容相似度或 Agent inference 自动 recall。

Human 以整个 episode 文档为主要治理单位：可以删除不希望保留的文件，必要时显式要求局部编辑。v1 不提供 index、deduplication、conflict resolution、summary、consolidation 或 archive lifecycle。

只有出现真实规模信号，例如同一 scope 的文档显著增长、Human 经常成组加载、冲突和重复妨碍判断，或 token 成本成为实际问题时，才值得设计由 Human 显式发起的 consolidation。它不应作为 v1 的自动后台行为。
