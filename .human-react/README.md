# Human ReAct Workspace

本目录是可嵌入宿主项目的运行协议。Human 维持宏观目标、context 和权限边界，显式选择 Task 表达本轮意图；Agent 在该 Task 内解释 user prompt 并运行 micro ReAct，并在完成或触及边界后返回 Human。

`orient / review / shape / plan / build` 表达独立的主要结果责任，不是固定 workflow stage；辅助分析在所选 Task 内完成。

## Minimum Invariants

1. Human 控制 macro loop、价值取舍和现实干预权限；
2. 所选 task 对本轮意图有最高解释优先级，只有 Human 明确重新选择才改变，不增加操作授权；
3. Agent 在 task 内解释 prompt，并完成该 task 明确允许且直接支持主要结果的辅助分析；实质改写在结果中披露，不因措辞冲突确认、切换或降低状态；
4. 未确认状态不能自动晋升为 Fact、Decision、Constraint 或权限；
5. Candidate、Resolved Choice、Planned Change、Authorized Change 与 Actual Change 保持分离；现实操作授权只来自 Human 明确请求或其无歧义引用且仍有效的既有委托；
6. Related to request、allowed to change 和 required to change 不是同一判断；
7. Agent 默认有界协调 context 冲突，material reconciliation 对 Human 可见；
8. Plan 不穷举完整 Effect Surface，Build 不因 patch 小或位于 Allowed Scope 就推断扩张安全；
9. 新目标、显著 scope expansion、新权限、重要风险或未授权 external effect 要求 Handback；
10. Task Closure 不生成必填 Reflection、未声明的持久化或自动下一步；只有 Human 显式选择 effectful Lens 才执行其 declared sidecar；
11. Build 以 verified reality 结束当前 delivery loop，只有 Human 能开启下一 loop。

详细定义见 [Core](core.md)。

## Module Map

| Module | Responsibility | Status |
| --- | --- | --- |
| [`core.md`](core.md) | 跨 task 运行语义 | v1 |
| [`tasks/**`](tasks/) | taxonomy、Prompt Contract 与 task prompts | 五个 task v1 |
| [`templates/**`](templates/) | 公共和 task-specific chat projection | 五个 projection v1 |
| [`loop.md`](loop.md) | Human-controlled macro 与 delivery loops | 设计文档 |
| [`memory/**`](memory/) | 手动加载的 episode-based project context | Memory Capture v1 |
| [`lenses/**`](lenses/) | Human 显式选择的 runtime modifier 与 declared sidecar | manual composition v1 |

## Runtime Composition

一个自包含 task contract 由当前 task prompt 与两层 projection 组成：

```text
tasks/<task>.md
+ templates/common.md
+ templates/<task>.md
```

`core.md` 是共享语义 owner，但不是没有 loader 的 harness 必须额外注入的文件；每个 task prompt 已保留 task 内请求解释、改写披露、完成度和权限边界的简短规则，三文件组合可以独立使用。

Human 可以在当前委托中手动附加适用 Lens：

```text
Task Contract
+ Human-selected Lens
+ resolved direct dependencies
→ specialized micro ReAct
```

普通 Lens 不改变所选 task 的主要责任、请求解释规则或 projection。带 `effects` 的 Lens 由 Human 显式选择后，只执行 metadata 声明的 protocol-owned sidecar，并通过现有 `Context` 投影 receipt。`tasks/*.md` 不分别导入 Lens；没有显式 Lens 时，上述基础组合、输出和工具自治原样运行。

## Project Context Layer

Memory 只在 Human 点名具体 capture 文件时手动进入当前 task。显式选择 [`memory-capture`](lenses/memory-capture.md) Lens 会为本轮创建一个 episode 文档；此前的 capture 只读，不自动 recall、合并、总结、更新或加载。

当前没有自动 loader、resolver、installer、registry、schema validator、Tool provisioning、Memory index 或 consolidation。Memory、recommendation、scope match 和 capability availability 都不能替 Human 选择 Lens；只有显式选择 effectful Lens，才授权其 metadata 声明的固定 sidecar。具体规范见 [Memory](memory/README.md) 和 [Lenses](lenses/README.md)。

## Documentation Ownership

- 共享运行语义归 [`core.md`](core.md)；
- task taxonomy 和 Prompt Contract 归 [`tasks/README.md`](tasks/README.md)，具体行为归对应 task 文件；
- 公共输出归 [`templates/common.md`](templates/common.md)，task-specific 输出归对应 template；
- task 关系归 [`loop.md`](loop.md)；
- Lens contract、索引与正式 Lens 归 [`lenses/**`](lenses/)；
- Memory load、capture 与治理归 [`memory/README.md`](memory/README.md)；
- 非运行时设计理由归 [`../design/`](../design/)。

其他文档只提供摘要或 task-local 操作化表达，不建立平行协议。
