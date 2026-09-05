# Lens Model

本文记录 Human ReAct 采用平铺 Lens 与显式能力组合的设计理由。它不是运行规范；正式 contract、metadata 和 Lens 内容以 [`.human-react/lenses/**`](../.human-react/lenses/) 为准。

## Why Lens Exists

五个 Task 有意保持通用和自包含：它们定义 Human 当前需要的结果、Agent 在委托内的责任以及 Handback 边界，却不可能预装所有分析立场、交付姿态和项目诊断知识。把这些内容直接写入 Task 会使通用 contract 膨胀，并混淆“要得到什么结果”和“以什么角度得到结果”。

Lens 填补的是这个中间层：它不是新的 Task，也不是一般 prompt，而是 Human 为当前 Task 选择的 context-bound runtime modifier。它提供更窄的 attention、evidence expectations 或 project coordinate system，仍由原 Task 负责完成结果。

```text
Task Contract
+ Human-selected Lens
+ resolved direct dependencies
→ specialized micro ReAct
```

这不违背 Task 内部由 Agent 自主 resolve 的原则。Human 只选择本轮值得采用的立场；Agent 仍在当前委托内决定如何观察、推理、调用已授权能力和验证。Lens 缩窄认识框架，不接管执行控制。

## Division Of Responsibility

| Construct | Primary Role | Does Not Provide |
| --- | --- | --- |
| Task | 结果类型、责任、工作策略、Handback 与 projection | 特定领域立场的完整集合 |
| Lens | attention、evidence expectations、交付 posture、项目坐标系及显式声明的窄 sidecar | 独立 Task Result、路由、状态或未声明授权 |
| Skill | 可复用的操作知识、过程和资源 | 当前目标、Task authority 或 Human choice |
| Tool | 对环境的 capability | 使用该 capability 的授权与理由 |
| Memory | Human-governed 的项目状态、事实、决定和偏好候选 | 行为函数或 Lens activation |

Lens 可以声明 Skill、Tool capability 和 reference 作为直接依赖。这让一个轻量立场能够借用较重的过程知识和能力，而不把它们复制进 Lens。声明只建立需要解析的关系：required Skill 缺失时降级或 Handback，不自动安装；Tool available 也不代表 authorized。`tools.required/preferred` 是稀疏需求，不是工具白名单；没有 Lens 时，Task 原有工具自治保持不变。

## Why Explicit-only

Human ReAct 的 macro loop 由 Human 维护。Lens 会改变 Agent 的注意分配、证据门槛和取舍方式，因此采用哪个 Lens 本身就是有意义的 framing choice。若由 Profile、Memory、scope match 或 Agent inference 静默决定，就会把推荐、历史状态或实现便利误当成本轮选择。

v1 因而只允许 `explicit-only`。Profile recommendation、Memory、scope match 或 Agent inference 不会使 Lens 生效。未选择 Lens 时，五个 Task 完全按自己的 contract 工作。

显式选择也为 effectful Lens 提供了可审计的授权来源：当 Lens 在 metadata 中声明固定 `effects` 时，Human 选择该 Lens 即表示要求这些 effect。授权只覆盖声明的 kind、root、timing、数量和生命周期；Lens 文件本身、工具可用性和任何未声明 effect 都不产生权限。

## Why Flat Files And Metadata

旧的三层分类目录把一个轻量语义差异变成了物理层级，也让新增 Lens 先面对 taxonomy navigation，而不是适用性和质量判断。当前规模下，平铺文件更容易发现、引用和人工组合：

```text
lenses/
├── README.md
├── memory-capture.md
├── poc.md
└── separated-analysis.md
```

分类保留在 `type` metadata 中，查询能力没有丢失，也避免路径成为永久兼容承诺。`id` 与文件名一致并全局唯一，使人工读取和未来可能的验证器都能使用同一个稳定标识。

Perspective、Posture 和 Project Trace 因而是 semantic types，而不是目录：

- `perspective` 表达可跨项目复用的认识角度或证据标准；
- `posture` 表达 Human 当轮选择的交付取舍；
- `project-trace` 表达真实项目特定的阶段模型和证据入口，并必须声明 `scope`。

Project Trace 只有在项目已经提供可靠的阶段模型、sources of truth、contracts、invariants 和 verification entry points 时才值得建立。先创建抽象的 Kotlin、production 或 security Lens 会把未经任务验证的知识包装成权威，因此 v1 不提供这些占位文件。

## Non-recursive Composition

Lens 只声明直接依赖，不依赖另一个 Lens。非递归组合有三个目的：

1. Human 能看见本轮实际采用的 framing；
2. 缺失能力的 Fallback 能在当前 Lens 边界内解释；
3. Lens 不会借助依赖链隐式扩大 context、工具面或授权。

复杂资料可以按需放入 `lenses/references/<lens-id>/`，但入口仍是单个平铺 Lens。没有内容时不创建空 package。

## Declared Sidecar Effects

普通 Lens 仍是纯 modifier。Effectful Lens 是窄例外：它可以在 Human 显式选择后产生 metadata 声明的 protocol-owned sidecar，但不能改变当前 Task Result 或业务 target。`memory-capture` 使用这一机制在固定 Memory 目录创建一个 episode 文档。

这不把 Lens 变成一般 capability sandbox：

- effect 必须预先声明，未列出的行为不获授权；
- Human selection 是授权来源，Lens 内容不能自我扩权；
- Tool requirement 说明完成 effect 需要什么，不枚举 Task 的全部可用工具；
- sidecar failure 通过当前 Task 的 `partial` 和 `Context` 披露，不自动安装能力或改写其他位置；
- Build 继续独占业务 target 的 durable intervention，Orient 与 Review 只允许声明范围内的 protocol sidecar。

v1 不增加工具 blacklist。Hard restriction 继续由 prompt、Task、harness permission 和 effect boundary 管理，避免用不稳定的工具名称代替真实 side-effect 约束。

## Epistemic Fit

Lens 让系统显式区分“结果契约”和“认识立场”。例如 `separated-analysis` 改善 observation、inference、conditional 和 hypothesis 的分离，但 Review 仍必须返回 Review Result；`poc` 缩窄 required-now 标准，但 Build 是否可以修改业务现实仍只来自 Human 的 Build request 和授权边界；`memory-capture` 只创建声明范围内的 sidecar，不把 Orient 或 Review 变成 Build。

这种结构支持 fallibilism：Lens 是可选择、可失配、可降级的认识工具，而不是隐藏的普遍规则。它也支持 situated knowledge：Project Trace 可以携带局部坐标系，但适用 scope 与 staleness 必须可见。最终结论仍受当前 evidence 约束，规范选择仍归 Human。

## V1 Deliberate Omissions

v1 不实现自动 loader、resolver、installer、registry、JSON Schema、Tool provisioning、通用 effect executor 或 Lens-to-Lens composition。metadata 先作为 Human 与 Agent 手动组合时的公共 contract；只有真实使用暴露出重复、歧义或验证成本后，才有理由增加程序化机制。
