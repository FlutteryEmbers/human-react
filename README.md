# Human ReAct

Human ReAct 是一套面向现代 Agent harness 的轻量 Prompt Workspace。它把 Human 视为宏观 ReAct loop 的中心：Human 维持目标、上下文和授权边界，选择本轮希望得到的结果；Agent 在该委托内自主观察、推理、调用工具、执行和验证，然后 Handback。

它不是线性 workflow、自动 task graph、Agent orchestration 平台或文档维护系统。

## Operating Model

```text
Orient / Review：形成 Scoped Explanatory Model 或 Evidence Context
                         ↓
Human：解释反馈并选择下一轮承诺层级
                         ↓
Shape / Plan / Build：结构化 Decision Space、形成 Execution Model 或改变 Reality
                         ↓
Human 决定是否发起 Orient / Review、重复当前 task 或改选其他 task
```

Task 表示本轮委托的结果类型和操作边界，不表示固定流程阶段，也不要求 Human 调度 Agent 的内部认知步骤。搜索、追踪、diagnosis、局部规划和验证都是 Agent 可以在当前 task 内按需使用的能力。

## Tasks

第一版包含五种彼此独立的委托模式：

| Task | Human 想获得的结果 | 状态转换 |
| --- | --- | --- |
| `orient` | 理解一个对象的背景、结构、机制、关系或不同观察角度 | Subject + Learning Need + Available Context → Scoped Explanatory Model |
| `review` | 对现状、问题、产物或判断形成 evidence-backed context | Existing Target → Evidence Context |
| `shape` | 对齐 Human 表达与系统语义，构造有边界的决策空间 | Human Expression + System Context + Agent Modeling Contribution → Decision Space |
| `plan` | 在授权边界内关闭 decision，明确计划修改面并形成可执行、可验证的单一路径 | Decision Space + Evidence + Delegation → Execution Model |
| `build` | 在实际授权范围内实现并验证目标，以现实反馈关闭当前 delivery loop | Requested Outcome + Authorized Change → Actual Change + Verification + Loop Closure Observation |

Human 可以直接选择、跳过或重复任意 task。选择 task 不证明其他 task 已完成，也不会扩大 user prompt 的授权。详细职责、自治边界、Closure Check 和完成条件统一见 [Tasks README](.human-react/tasks/README.md)。

完整的多尺度循环和 Mermaid 图见 [实际循环](.human-react/loop.md)。

## Epistemic Orientation

Human ReAct 不是一套完整的哲学认识论或认知科学模型，但它把一种认识论取向操作化为 Human–Agent 协作协议：task 不描述 Agent 内部必须依次经历的思考步骤，而是规定本轮要形成哪一种认识、承诺或现实状态，以及这些状态以什么证据、判断和授权为边界。

| Task | 认识与实践作用 |
| --- | --- |
| `orient` | 围绕 Human 的 Learning Question 形成 Scoped Explanatory Model，区分 target-specific fact、general model、interpretation、perspective 与 unknown，但不形成评价或承诺 |
| `review` | 从现实、产物或既有判断中取得 evidence，区分事实、推断、假设、条件分析与未知，形成可供继续判断的 Evidence Context |
| `shape` | 在保留 Human Anchor 的前提下，对 Human 表达、系统语义和 Agent 建模贡献进行可修正的解释，显化候选、约束、区分标准与需要 Human 决定的例外 |
| `plan` | 从当前请求重新建立 delegation，将 Decision Space 与 Evidence 转化为单一 Execution Model，体现从认识到 Planned Change 的过渡 |
| `build` | 依据实际授权介入 Reality，以 Verification 约束完成声明，并用 Loop Closure Observation 将当前 delivery loop 的现实结果和 material delta 返回 Human |

大循环由 Human Expression、Working Model、Evidence、Commitment 与 Reality 之间可能存在的 material misalignment 驱动。Misalignment 不只包括两个事实主张不能同时成立的逻辑矛盾，也包括语义歧义、claim 与 evidence 冲突、goal 与 constraint 张力、plan 与实际条件偏离、expected 与 actual gap，以及 desired action 与当前授权不一致。事实冲突可以调查，语义歧义可以修正，实践方案可以调整；价值冲突需要 Human 选择，权限缺口则必须 Handback，不能都被包装成等待 Agent 发现的客观真相。

这些冲突默认不会被路由给 Human 逐项仲裁。所有 task 共享 `Bounded Reconciliation`：Agent 在当前 authority 内自主解释、验证、选择 working basis 或保留有意义的差异，并继续完成当前 task。实质影响结果的协调必须向 Human 披露；只有创建新的规范性决定、scope、权限或风险承诺时才必须 Handback。

```text
Potential Misalignment
Human Expression / Working Model / Evidence / Commitment / Reality
        ↓
Orient / Review / Shape
解释或暴露事实、语义、规范、权限与实践张力
        ↓
Human + Agent
在各自 authority 内修正理解或关闭 decision
        ↓
Plan / Build
形成承诺并对 Reality 进行授权干预
        ↓
Verification
产生新的 observation 与 evidence
        ↓
Warranted Provisional Closure
        ↺ 新 evidence 或新目标可以重新打开循环
```

这是一种常见的信息流，不是系统自动执行的 task sequence。每个 task 仍可被直接选择、跳过或重复。

Shape、Plan 与 Build 之间使用轻量 `Commitment Grounding`，避免把认知可能性、实践选择和现实授权混为一体：

```text
Candidate consideration ≠ Human Decision
Resolved Choice ≠ Build Authorization
Planned Change ≠ Authorized Change ≠ Actual Change
```

Shape 中未被排除的普通 means-level Candidate，可以在 Human 显式发起 Plan 后由 Agent 在当前边界内评估和关闭；这不表示 Human 已逐项接受 Candidate。Plan 的 Change Surface 只是 Planned Change；只有明确的 Build execution request 所引用或无歧义延续、且仍符合当前 scope、permission 和 risk boundary 的部分，才成为 Authorized Change。

循环同时包含两种相反的 `direction of fit`：Orient 使 Human 获得有边界、可修正的 explanatory model；Review 使 Working Model 接受 Reality 校正，是 `model ← reality`；Build 使 Reality 在实际授权内接近 Human 的 Desired Effect，是 `authorized intention → reality`；Shape 与 Plan 位于判断和行动之间，区分规范选择并建立实践承诺。因此系统既包含 understanding、truth-seeking，也包含经过授权的 world-making。

这个模型隐含几项基本立场：Reality 不由 Human 或 Agent 的表达单独决定，判断需要接受可观察 evidence 的约束；当前理解是可修正的 working view，不能把 Assumption、Conditional 或 Candidate 静默升级为事实；认识的价值不仅在于描述，也在于支持行动并经受结果验证；认知分布在 Human、Agent、conversation、工具、证据与现实对象构成的循环中，而不是完全属于单一主体。

Truth 在这里是持续校准 inquiry 的 `regulative ideal`，不是某轮 task 可以永久占有的终止状态。一次循环的闭合只表示当前问题已得到足够回答、重要 claim 没有超过 evidence boundary、需要 Human 决定的 choice 与授权边界已经可见、实际变化经过了相称验证，并且 control 已经 Handback。这个 `Warranted Provisional Closure` 可以被后续 evidence、Reality change 或新目标重新打开。

认识状态、规范承诺和执行授权仍然彼此独立。Evidence 可以支持“当前是什么”或“为什么发生”，但不能单独决定“应当追求什么”、Human 应接受什么风险，或 Agent 是否获得改变现实的权限。Human 维持目标、价值取舍和外部效果授权；Agent 只在当前委托内自治。

这一认识论定位不是完成度声明。当前 `orient`、`review`、`shape`、`plan` 与 `build` 已具备第一版的解释、证据纪律、语义对齐、decision closure、现实执行和可错性边界，但知识来源分离、证据质量、evidence 到 finding 的推理根据、主动反证、描述性事实与规范性决定的进一步分离，以及跨 task 保留 claim 的来源、范围和失效条件，仍是后续需要验证和完善的方向。

## Core Boundaries

Human 控制宏观循环和外部效果授权；Agent 控制当前委托内的 micro ReAct。task 之间不自动转换，任何名称、前序结果、Memory 或 Lens 都不能代替当前授权。完整不变量见 [Workspace README](.human-react/README.md)，task 自治、Handback 与 Closure 规则见 [Tasks README](.human-react/tasks/README.md)，公共输出边界见 [Common Chat Projection](.human-react/templates/common.md)。

## Current Status

当前已经形成的是文档级设计：

- [`tasks/README.md`](.human-react/tasks/README.md) 定义 task 体系；
- [`loop.md`](.human-react/loop.md) 描述 Human 维持的实际循环；
- [`templates/README.md`](.human-react/templates/README.md) 提供 projection 导航，[`templates/common.md`](.human-react/templates/common.md) 定义公共输出协议；
- [`memory/**`](.human-react/memory/) 与 [`lenses/**`](.human-react/lenses/) 描述尚未接入的 Project Context Layer。

`orient`、`review`、`shape`、`plan` 与 `build` 均已有第一版 task prompt 和 chat projection。项目没有实际 Project Profile、Topic Memory、正式 Lens、自动加载、Memory 写入、adapter、schema、生成器或安装器。

## Directory

```text
.human-react/
├── README.md
├── loop.md
├── tasks/
│   ├── README.md
│   ├── orient.md
│   ├── review.md
│   ├── shape.md
│   ├── plan.md
│   └── build.md
├── templates/
│   ├── README.md
│   ├── common.md
│   ├── orient.md
│   ├── review.md
│   ├── shape.md
│   ├── plan.md
│   └── build.md
├── memory/
│   ├── README.md
│   └── topics/
│       └── README.md
└── lenses/
    ├── README.md
    ├── perspectives/
    │   └── README.md
    ├── postures/
    │   └── README.md
    └── project/
        └── README.md
```

## Documentation Ownership

| Document | Sole Responsibility |
| --- | --- |
| [`README.md`](README.md) | 项目定位、公开入口、当前状态和文档导航 |
| [`.human-react/README.md`](.human-react/README.md) | 可嵌入 Workspace 的入口、最小不变量和模块状态 |
| [`tasks/README.md`](.human-react/tasks/README.md) | task 共同规则、详细职责、边界和完成条件 |
| [`loop.md`](.human-react/loop.md) | Human 维持的宏观循环、同-task 循环和反馈路径 |
| [`templates/README.md`](.human-react/templates/README.md) | chat projection 的目录导航、组合方式和文件职责 |
| [`templates/common.md`](.human-react/templates/common.md) | Task Result 的公共骨架、状态、语义边界和 material disclosure |
| [`memory/README.md`](.human-react/memory/README.md) | 未接入的 Project Memory 候选模型 |
| [`lenses/README.md`](.human-react/lenses/README.md) | 未接入的 Lens 分类、联动和共同边界 |

详细文档只能定义其负责的语义；其他 README 应链接而不是复制。

## Relationship With Workflow Lite

[`sandbox/workflow`](sandbox/workflow/) 中的 Workflow Lite 只作为结构和设计思路参考。Human ReAct 借鉴显式 task、规范输出、Lens 和外部化上下文的价值，但不是兼容版本，也不继承其 task taxonomy、session lifecycle、artifact、persist、sync 或 archive 体系。

核心差异是：

> Human 维护目标、context、授权和反馈循环；task 表达本轮希望更新的状态，Agent 负责委托内部的 micro ReAct loop。
