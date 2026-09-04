# Human ReAct Tasks

本目录定义 Human ReAct 第一版五个顶层 task。本文是 task 体系的唯一总说明，负责共同规则、详细定位、操作边界与完成条件。`orient`、`review`、`shape`、`plan` 与 `build` 均已有第一版 task prompt。

正式 task：

```text
orient / review / shape / plan / build
```

搜索、阅读、追踪、试验和更新理解是所有 task 都可以按需使用的内部能力。`orient` 的独立结果是面向 Human 的 Scoped Explanatory Model，不是对这些内部活动的路由。

目录内容：

- [`orient.md`](orient.md)
- [`review.md`](review.md)
- [`shape.md`](shape.md)
- [`plan.md`](plan.md)
- [`build.md`](build.md)
- [Review、Gap Analysis 与 Diagnosis](review-and-diagnosis.md)

## 总体模型

五个 task 分别维护不同状态：

| Task | 状态转换 | 核心问题 |
| --- | --- | --- |
| `orient` | Subject + Learning Need + Available Context → Scoped Explanatory Model | 我应该怎样理解这个对象，哪些背景、机制与视角真正相关？ |
| `review` | Existing Target → Evidence Context | 现状、问题、产物或当前判断意味着什么？ |
| `shape` | Human Expression + System Context + Agent Modeling Contribution → Decision Space | 我们如何理解问题，又面对哪些决策？ |
| `plan` | Decision Space + Evidence + Delegation → Execution Model | 在当前授权内应选择哪条可执行路径？ |
| `build` | Requested Outcome + Authorized Change → Actual Change + Verification + Loop Closure Observation | 怎样在授权范围内改变现实，并关闭当前 delivery loop？ |

Task 可以被直接选择、跳过或重复。选择 task 不证明前序 task 已完成，也不自动扩大 user prompt 的授权。

Review 可以通过 bounded non-mutating observation、已有检查或隔离的临时实验接触 Reality；Build 独有的是 durable 或 material intervention authority。Observation capability 不产生修改权限，Build authority 也不把执行结果自动升级为 truth。

## Task Prompt Contract

Task prompt 是 Agent 的本轮行为契约，不是 user input form、固定执行流程或输出模板。它只负责定义：

- 本轮需要产生的状态转换；
- Agent 在当前 task 内可以自主完成的工作；
- 不得跨越的目标、scope、权限和外部效果边界；
- 必须 Handback 的条件；
- 可观察的完成条件。

Task prompt 与 Result Template 的关系是：

```text
User Prompt + Conversation
+ Task Prompt             behavior and authority boundary
        ↓
Agent Micro ReAct
        ↓
Result Template           chat projection only
```

Task 文件不得复制公共 Result Packet。输出字段、条件 section 和表达规则统一由 [`templates/**`](../templates/) 负责。

### Minimum Common Template

五个 task-specific 文件统一使用以下最小骨架：

```markdown
# Task: <name>

> <Input State> → <Result State>

## Shared Contract

- 当前 user prompt 决定实际 target、scope 和授权；
- task 名称只决定本轮结果类型，不提供额外权限；
- 为形成当前结果所需的局部观察、推理、工具调用和验证可以自主完成；
- 当前 context 的冲突默认由 Agent 在委托边界内自主协调；实质影响结果的 reconciliation 必须投影；
- 不改变目标、不显著扩大 scope、不替 Human 作出重要取舍；
- 触及边界时 Handback，不静默切换 task；
- 完成后执行内部 Closure Check，但不输出必填 Reflection。

## Responsibility

<!-- 本 task 唯一负责产生什么结果。 -->

## Working Policy

<!-- 必须完成的语义工作、允许吸收的内部操作和自适应原则；不规定固定步骤。 -->

## Boundaries

<!-- 不负责的结果、禁止的外部效果和不得推断的授权。 -->

## Handback

<!-- 哪些目标、scope、权限、风险或 Human decision 要求停止。 -->

## Complete When

<!-- 哪些可观察语义条件成立时，本 task 可以结束。 -->

## Result Projection

使用 `../templates/<task>.md`。
只投影 Task Result，不输出内部推理或工具流水账。
```

`Shared Contract` 是有意保留的最小重复，使单个 task 文件被现代 harness 独立加入 context 时仍能保持核心边界。其余 section 只写 task-specific delta，不复述本 README、宏观 loop 或 Project Context Layer。

### Section Semantics

- `Responsibility` 定义唯一结果，不承担 task routing；
- `Working Policy` 描述证据、决策和适应原则，不规定工具调用顺序；
- `Boundaries` 说明当前 task 不拥有的结果与外部效果；
- `Handback` 只列真正要求停止 micro ReAct 的边界事件；
- `Complete When` 使用语义结果而不是“读过文件、跑过命令、填满 section”等过程条件；
- `Result Projection` 只引用对应 template，不复制字段。

### Prompt Design Boundaries

Task prompt 默认直接使用当前 user prompt 与 conversation，不要求 Human 填写 `Target`、`Mode`、`Depth`、`Thread`、`Lens` 或 `Memory` 等结构化 intake。请求清晰时直接工作；缺失信息能在现有 scope 与权限内安全补足时自主补足；只有会实质改变结果时才 Handback。

第一版 task prompt 不包含：

- 大段 `Use When / Do Not Use When` 路由表；
- front matter、mode 或 artifact taxonomy；
- 固定的内部认知步骤或逐阶段思考脚本；
- 完整 self-audit checklist、persona 或大量示例；
- 强制的下一 task 建议、routing table 或自动转换规则；
- Memory/Lens loading、binding 或自动写入；
- 公共输出骨架和 task-specific Result Template 内容。

示例与评测案例未来应放在 prompt 之外，用来验证 task 是否稳定，而不是成为默认 context。

## Bounded Reconciliation

`Bounded Reconciliation` 是所有 task 共享的 Agent micro ReAct 原则。当 Human expression、requested target、scope、delegation、working model、evidence、constraint、decision、plan 或 reality 之间出现冲突时，Agent 默认在当前目标、权限、风险和 task responsibility 内自主协调，而不是立即要求 Human 裁决或切换 task。

```text
Detect conflict
→ Normalize claims / context
→ Perform bounded investigation
→ Reconcile within current authority
→ Continue current task
→ Disclose material reconciliation
```

Reconciliation 可以产生四种 working state：

| State | Meaning |
| --- | --- |
| `resolved` | 冲突已通过语义、role、scope、版本或新 evidence 解释或关闭 |
| `provisional` | Agent 采用一个 working basis 继续，但仍有会影响判断的未知 |
| `preserved` | 冲突具有信息价值，被保留为 Candidate、Pressure Point 或 Open Decision |
| `unresolved` | 当前无法形成 task 所需的可靠 working basis |

Agent 应先确认冲突内容是否在讨论同一 proposition、scope、环境和时间点，再根据当前 task 问题判断哪些 context 适用。不建立“代码 > 测试 > 文档”之类全局顺序，不用多数票或更新时间单独决定权威。

Reconciliation 可以选择当前 task 的 working basis，但不能因此创建新的 Human intent、产品或领域语义、source-of-truth policy、scope、权限或风险承诺。Agent 对 Human 请求采用的解释如果会实质改变 Change Surface、执行权限或结果边界，必须作为 material reconciliation 披露；能够采用满足请求的更窄解释时，不得用体系完整性或实现便利扩大 scope。触及这些边界时必须 Handback，但应先返回已形成的 working interpretation、推荐与冲突结构。

`provisional`、`preserved` 或 `unresolved` 不会机械导致 `Status: blocked`。只有当前 task 无法形成任何有用结果时才 blocked。Routine 且不会改变 Outcome、承诺、现实变化或 Human 判断的局部冲突无需投影；其他情况按 [Templates README](../templates/README.md) 的 material disclosure 规则返回 Human。

各 task 使用相同能力，但具有不同的 resolution depth：

| Task | Conflict Focus | Authority-bounded Result |
| --- | --- | --- |
| `orient` | target-specific evidence、general model、Human assertion 与解释性综合 | 形成 scoped working explanation；不用通用知识覆盖项目现实 |
| `review` | 相互冲突的 evidence、claim 和原因解释 | 形成 evidence-backed 或 provisional conclusion；不创建新规范 |
| `shape` | Human language、system meaning、Current Take、constraint 和 candidate model | 消除误解，或将有意义的差异保留为 Decision Space |
| `plan` | requested target、scope、delegation、inferred Change Surface、implementation strategy、dependency 和 verification | 在 current delegation 内关闭 means-level decision；需要 Human 决定的冲突 Handback |
| `build` | execution model、code、test、tooling 和 runtime reality | 吸收授权内局部偏差；保持 requested outcome、authorized scope 与已建立 Compatibility Boundary |

## Commitment Grounding

`Commitment Grounding` 是 Shape、Plan 与 Build 共享的轻量承诺边界。它不增加 task、审批流程、状态机或公共必填字段，只约束不同状态何时可以晋升：

```text
Candidate consideration ≠ Human Decision
Resolved Choice ≠ Build Authorization
Requested Outcome ≠ Planned Change ≠ Authorized Change ≠ Actual Change
```

Shape 中未被 `Rejected` 或 `Deferred` 的 Candidate 默认可以被后续 Plan 考虑，但仍是未承诺 proposal。Human 显式发起 Plan，构成对当前 goal、scope、constraint 和 risk boundary 内 means-level choice 的 contextual delegation；它不确认每个 Candidate，也不授权改变现实。

```text
Shape Candidate
+ not Rejected / Deferred
+ Human explicitly invokes Plan
+ current goal / scope / constraint remains unchanged
→ eligible for Plan closure
```

Agent 自己提出的 Candidate、Decision Criteria 或 closure classification 不能共同创造新的目标、价值标准、scope、权限或风险接受。选择会改变 Desired Effect、产品或领域语义、scope、external contract、Compatibility Boundary、重要风险或权限时，必须显式保留为 `Human Decision Required`，不能因 Human 尚未反对而关闭。

Plan 形成的是 `Planned Change`。只有当前 Build execution request 明确引用或在上下文中无歧义地延续该 Plan，并且仍处于当前 scope、permission 和 risk boundary 内时，相关部分才成为 `Authorized Change`：

```text
Authorized Change
= current Build request
∩ unambiguously referenced current Plan
∩ current scope / permission / risk constraints
```

当前 prompt 的缩小、排除和修正优先于旧 Plan。task 名称、Plan 的 `complete`、Human 没有反对、Memory 或 Lens 都不能单独产生 execution authority。新 evidence 可以修正 Current Take、Decision Space 或 Execution Model，但不能扩大 action authority。

这些判断默认在 task 内部完成。只有晋升依据不明显、发生 material reclassification、解释改变 planned/actual surface，或需要 Human 决定时，才通过 Basis、Reconciliation 或 Human Attention 投影。

## Compatibility by Exception

`Compatibility by Exception` 是跨 Shape、Plan、Build 和 Review 的轻量边界原则。它不创建 compatibility task、默认 policy、固定状态机或公共 schema，而是只在兼容性会实质改变 Human 判断或现实结果时进入当前 task：

```text
Detect material Compatibility Surface
→ Establish Boundary and Basis when needed
→ Plan chooses Mechanism within that Boundary
→ Build preserves the Boundary while executing
→ Review audits observed effect against the Boundary
```

这里不设置默认 preserve 或默认 break：

```text
No established Compatibility Obligation
≠ preserve required
≠ breaking authorized
```

以下情况构成 material trigger：存在已知外部消费者或 public API、CLI、protocol；涉及持久化数据、配置格式或 serialization；改变 externally observable behavior；或者 preserve / break 会实质改变 scope、成本、风险或 acceptance。普通内部重构、等价实现替换以及不影响 material surface 的变化不触发兼容建模或输出。

Shape 是 compatibility boundary 的主要建模阶段。它识别相关 Surface，并在需要时暴露 Boundary 与 Basis；Surface 无法从当前 context 或局部只读检查确认时保持 Unknown，不推断不存在消费者。Plan 不要求 Shape 前置，也不主动寻找未知消费者或创建兼容义务；但当 Planned Change 明显改变已知 external contract，而 Boundary 尚未建立时，Plan 不能把缺少 preserve Constraint 当作 breaking authorization，必须返回已有局部方案并要求 Human 决定。Boundary 建立后，Plan 可以在 current delegation 内关闭 adapter、migration、dual-read、alias、cutover 等 Compatibility Mechanism。

Build 必须保持当前 Authorized Change 所引用的 Compatibility Boundary；具体执行规则由 [Build task](build.md) 定义。Review 继续以普通 evidence、gap 和 diagnosis 审计 observed effect 是否符合 Boundary，不增加 Review-specific compatibility 模式。

`Compatibility Surface`、`Boundary`、`Basis` 和 `Mechanism` 是设计语义，不是固定字段。没有 material compatibility 内容时，整个 trace 被省略；不得生成空兼容占位或为潜在未知消费者展开开放式 compatibility archaeology。具体投影规则由 [Templates README](../templates/README.md) 定义。

## Established / Conditional Separation

Task 结果需要区分当前已成立或已承诺的状态，与依赖未确认前提、候选选择或未来条件的推演。条件推演不得自动更新 task 所维护的状态，也不构成 Human decision 或执行授权。

`Assumption` 与 `Conditional` 不同：

- `Assumption` 是为继续当前工作而暂时采用的未验证前提；
- `Conditional` 不接受该前提，只说明“如果它成立，会推出什么”。

该分离在不同 task 中的候选映射为：

| Task | Established / Committed State | Conditional Projection | 晋升条件 |
| --- | --- | --- | --- |
| `orient` | Target-specific Fact / Scoped Explanatory Model | General Model / Conditional Explanation | target evidence 建立其适用性 |
| `review` | Evidence-backed Finding / Diagnosis | Premise-Conditional Analysis | 新 evidence 确认 premise |
| `shape` | Evidence-backed Context / Human Decision | Candidate Direction / Conditional Intent | Human 明确决定，或 Plan 在 current delegation 内形成 Resolved Choice |
| `plan` | Chosen Approach / Execution Model | Contingency / Conditional Branch | 条件被验证，或所需 Human 取舍已完成 |
| `build` | Actual Change / Verification | Possible Fix / Future Change | Human 新授权后实际执行并验证 |

`orient` 已实现 target-specific fact、general model、interpretation、perspective 与 unknown 的分离；`review` 已实现 Premise-Conditional Analysis；`shape` 已实现 evidence-backed context、Human Decision、Candidate Direction 与 Conditional 的分离；`plan` 已实现 Chosen Approach、Resolved Choice 与 Execution Model，但上表的 Contingency / Conditional Branch 仍只是候选设计。`build` 已实现 Actual Change、Verification 与 Loop Closure Observation；Possible Fix / Future Change 仍不能在没有新授权时晋升为现实状态。

## Task Closure

每个 task 在 Handback 前都应进行轻量的内部 Closure Check：比较 requested outcome 与 actual outcome，确认已完成和已验证的部分，识别未完成、跳过或偏离的内容，并判断本轮是否产生了真正可复用的新认识。

Closure Check 不是新的 task、公开 checklist 或必填 `Reflection` 字段。它只用于校准 `Status`、结果内容和 Human Attention；没有对 Human 判断有价值的信息时不单独投影。需要对产物进行更深入、独立的事后评价时，仍由 Human 另行发起 review。

## Orient

### 职责

将 Subject、Learning Need 与 Available Context 转化为面向 Human understanding 的 Scoped Explanatory Model。Orient 解释关键术语、结构、机制、关系和必要背景，可以补充少量 materially different perspective，但不形成评价、决策、计划或现实变化。

```text
Orient changes Human understanding.
Review establishes evidence-backed judgment.
Shape constructs a Decision Space.
Plan forms an Execution Model.
Build changes Reality.
```

### 允许的内部操作

- 从 conversation 识别 Subject、Learning Question 和必要的 Intended Understanding；
- 搜索、阅读、追踪和观察与解释直接相关的 context；
- 建立概念、组成部分、机制、过程与关系的 mental model；
- 提供直接改善当前理解的背景知识；
- 提供少量能改变关注结构的 Alternative Perspective；
- 区分 target-specific fact、general model、interpretation、assumption、conditional 和 unknown；
- 对冲突的解释依据使用 Bounded Reconciliation。

这些操作只服务解释结果。Background 是否值得保留由它对当前 Learning Question 的解释价值决定；主题仍可继续扩展不代表当前 Orient 尚未完成。

### 边界

Orient 不负责：

- 审计 correctness、fitness、gap 或 diagnosis；
- 把 perspective 转换成 Candidate comparison、recommendation 或 Human Decision；
- 形成 Decision Space、Execution Model、Change Surface 或 execution authorization；
- 修改代码、文档、配置、运行状态或外部系统；
- 生成百科式 background packet、reading list 或完整检索记录；
- 自动持久化结果或开始其他 task。

一项请求的归属由本轮预期结果决定：详细说明调用链如何工作属于 Orient；判断调用链为什么没有形成预期 journey 属于 Review；讨论调用链应该如何重新建模属于 Shape；形成修改面与验证方案属于 Plan；修改并验证属于 Build。

### 完成条件

- Outcome 已直接回答 Learning Question；
- 关键术语、结构、机制或关系已经形成 Human 可用的 Understanding Model；
- Relevant Background 只保留对当前解释有实际作用的内容；
- target-specific fact、general model、interpretation、perspective 和 unknown 没有被混合；
- Alternative Perspective 已按需说明其贡献与边界，没有变成设计选择；
- material conflict、Assumption、Conditional 与解释边界已按需可见；
- 没有产生 verdict、commitment、authorization 或现实修改；
- 结果已 Handback 给 Human。

`Status: complete` 只表示当前 Learning Question 已获得最小充分的 Scoped Explanatory Model，不表示 Subject 已被穷尽或后续判断已经完成。详细行为见 [Orient task](orient.md)，输出规则见 [Orient projection](../templates/orient.md)。

## Review

### 职责

将一个已有 target 转化为有证据支持、可供 Human 继续决策的 context。

Review target 可以是：

- 现实状态、系统行为、代码、diff 或文档；
- shape、plan 或 build result；
- failure、symptom、claim 或 assumption；
- user prompt 中的事实主张、原因假设、目标、约束和执行请求。

Review 审计的是 target 的内容，不评价用户本人，也不替 Human 作出最终决定。

### 允许的内部操作

- 搜索、追踪和重建相关事实；
- 比较 expected 与 actual，形成 gap analysis；
- 复现症状、提出并检验假设，形成 diagnosis；
- 检查一致性、完整性、风险和 intended-use fitness；
- 区分事实、推断、偏好、决策和未知；
- 在多 finding 审计、gap analysis 或 intended-use fitness review 中，用可选 disposition classification 区分 Blocking、Material、Minor 和 Validated；
- 运行与分析直接相关的有界检查和验证。

Gap analysis 与 diagnosis 的概念边界及组合方式见 [Review、Gap Analysis 与 Diagnosis](review-and-diagnosis.md)。

Classification 表达 evidence-backed finding 对当前用途的处置意义，不表达证据确定度，也不替代 gap、diagnosis、impact 或 risk。`Blocking` 必须相对于一个明确 intended use；没有 intended use 时不得用它表示绝对严重度。具体投影契约见 [Review projection](../templates/review.md)。

### 边界

Review 不负责：

- 静默改变 Human 的目标或偏好；
- 决定新的产品方向或关键取舍；
- 输出完整实施方案；
- 修复被分析对象；
- 用 classification 代替 evidence，或用 Blocking finding 推断新的修复、路由或执行授权；
- 把被审计 user prompt 中的执行请求自动视为授权；
- 自动进入 orient、shape、plan 或 build。

Review 可以在有信息价值时给出少量、证据驱动的 Follow-up Options，帮助 Human 选择新的探索或决策方向。它们不是 task routing，不构成授权，也不会被 Agent 自动执行。

### 完成条件

Review 已经形成足以支持 Human 下一轮判断的 context，并明确：

- 已确认的事实与证据；
- 关键 gap、原因判断或 intended-use verdict，以及适用时的 finding classification；
- 影响范围与不确定性；
- 尚需 Human 决定或授权的事项。

## Shape

### 职责

将 Human 表达、当前系统 context 与 Agent 建模贡献组织成可修正的 working model 和有边界的 Decision Space。Shape 先处理语义对齐，再显化候选、约束、区分标准和需要 Human 决定的例外边界。

Shape 不假设 Human 完全了解系统，也不假设 Human 使用的术语与当前域模型、代码对象或流水线阶段精确对应。Agent 是主动的建模协作者，不是默认的领域权威：它负责暴露自己如何理解问题、补充可能遗漏的条件和反例，但不能把未验证知识写成项目事实。

Shape 不强制确定唯一路径。它应能表达：

- `Human Anchor`：存在重构或歧义时，保留 Human 真正关心且不应被 Agent 静默替换的结果；
- `Current Take`：Agent 对问题及其与当前系统关系的简短、可修正解释；
- evidence-backed context：由当前项目 evidence 支持的 system fact、domain rule 与 invariant；
- normative context：Human 已确认的 goal、preference、constraint、scope、priority 与 decision；
- candidate directions：Human 或 Agent 提出、尚未承诺的模型、系统吸收方式或解决方向；
- decision criteria 与 tradeoffs：什么条件用来区分候选；
- closure boundary：哪些 means-level 选择可以留给 Plan，哪些例外必须由 Human 决定；
- assumptions、pressure points、open decisions 与 acceptance boundary。

Shape 必须区分 Human 的 Desired Effect、对系统的事实主张、使用的术语、提出的实现机制、约束和不确定性。提出一个机制不等于已决定采用它；Agent 对语义的修正也不等于 Human 改变了目标。

歧义应按影响而不是按数量处理：能在当前 scope 内通过 context 或局部只读检查消除时自主消除；不改变当前方向时使用 `Assumption`；不接受前提、只延展其逻辑时使用 `Conditional`；如果多种理解会导致 materially different 的目标、scope 或系统边界，则必须暴露差异并在无法消解时 Handback。

Agent 不只复述 Human context。它可以主动提出少量会改变当前 focus 的遗漏条件、反例、冲突和候选模型。未证实的事实前提属于 `Assumption` 或 `Unknown`，条件推演属于 `Conditional`；`Candidate` 只表示能够作为整体被接受、拒绝或比较的最小 decision-relevant proposal。

同一方案中需要一起接受的组成部分归入一个 Candidate。只有可独立决定、相互替代或需要分别确认时才拆分为多个；互斥候选需要 Pressure Point 和适用的 Decision Criteria，依赖或兼容关系仅在影响判断时用自然语言说明。Candidate 集合默认是当前有用但非穷尽的 decision space；只有高影响场景存在 material framing risk 时，才需要说明集合边界或补充一个 materially different alternative。

Candidate 始终是未承诺 proposal。未被 Human `Rejected` 或 `Deferred` 的普通 means-level Candidate 可以在 Human 显式发起 Plan 后被重新评估并关闭，不需要逐项确认。Shape 不把这种 eligibility 表达成授权，也不向 Plan 传递 permission token。

只有选择会改变 Desired Effect、领域或产品语义、scope、external contract、风险接受或权限时，Shape 才将其标记为 `Human Decision Required` 并放入 Human Attention。Agent 提出的 Decision Criteria 默认仍是建模贡献；如果选择取决于 Human preference、价值或风险偏好，不能把该 criterion 当成已确认 Constraint。

Human 可以在 Shape 中直接作出任何决定。Plan 仍必须根据发起 Plan 的当前 prompt、已确认边界与 evidence 重新建立 current delegation；Shape 的 Candidate 或分类本身不构成 decision 或 authorization。

### Compatibility by Exception

Shape 只在出现 material trigger 时建模 compatibility。它通过当前 conversation、项目 evidence 或局部只读检查识别 Compatibility Surface；未知的 Surface 保持 Unknown，不据此推断不存在外部依赖。

Shape 使用既有 Decision Space 语义表达 Boundary：Human 或项目明确要求 preserve 时使用 `[Constraint]`；Human 明确接受 breaking change 时使用 `[Decision]`；尚未承诺的 boundary proposal 使用 `[Candidate]`；preserve / break 会改变 contract、scope、成本、风险或 acceptance 时，使用 `[Open]` 与 `Human Decision Required`。重要 Boundary 同时用简短 Basis 说明其来自 Human、项目规范或 evidence。

Compatibility Mechanism 只是 Boundary 之下的 means-level Candidate。Shape 可以暴露有决策价值的机制方向，但不形成 adapter、migration、dual-read、alias 或 cutover 的实施承诺。没有 material Surface 时不产生兼容内容。

### 多轮更新

Shape 可以在多轮 user prompt 中重复进行。下式描述 Agent 内部维护的 decision space，而不是要求每轮输出完整快照：

```text
Current Take + Current Decision Space
+ New Human Context
+ System Evidence
+ Agent Modeling Contribution
+ Human Decisions / Corrections
- Invalidated Assumptions
= Updated Take + Decision Space Delta
```

每轮应识别 Human 新增内容是候选表达、明确决定、纠正、约束还是事实主张。“我觉得”、“可能”、“暂时”等探索性表达不自动成为 Decision。连续对话本身是 working context；Shape 不维护独立 memory artifact。

Shape 的 focused、delta-first、summary-on-demand 输出规则由 [Shape projection](../templates/shape.md) 定义。普通回合优先只投影一个 primary focus、`Current Take` 的实质修正与少量 Decision Space delta。`complete` 表示本轮要求的语义对齐或 decision-space update 已形成，不表示所有候选已收敛或 Plan 已 ready。

### 允许的内部操作

- 重建并规范化当前问题、领域概念、目标和边界；
- 在必要时保留 Human Anchor，并形成可修正的 Current Take；
- 对 Human 术语与当前系统概念进行有边界的 semantic grounding；
- 在 material trigger 出现时识别 Compatibility Surface，并暴露 Boundary、Basis 与未决影响；
- 区分事实、领域规则、偏好、约束、候选和决定；
- 提出有限候选方向、区分标准和取舍；
- 用遗漏条件或反例对当前 focus 进行轻量 pressure test；
- 区分领域知识与当前系统的 required、simplified、deferred 或 out-of-scope 吸收决定；
- 合并 Human 的补充、修正和确认；
- 暴露冲突、失效假设和开放决策；
- 为理解 context 进行必要的局部搜索或检查。

### 边界

Shape 不负责：

- 把未经验证的事实主张升级成确定事实；
- 把 Current Take 写成 Human Intent，或通过纠正术语静默替换 Human 关心的结果；
- 把 Agent 的通用建模知识当成当前项目事实或默认领域权威；
- 默认 preserve 或 break，或为推测性的未知消费者展开开放式 compatibility archaeology；
- 静默关闭标记为 `Human Decision Required` 的选择；
- 完成开放式根因调查；
- 设计详细实施步骤和验证顺序；
- 修改代码、配置或其他现实对象；
- 自动进入 orient、review、plan 或 build。

### 完成条件

Shape 不要求所有候选都被关闭。本轮可以在以下条件成立时 Handback：

- Human 关心的结果已被保留，需要时 Human Anchor 已显式投影；
- Current Take 已能准确表达当前理解，关键术语映射和实质歧义已消解或暴露；
- evidence-backed fact、Human normative context、candidate、assumption、conditional 和 Human decision 没有混合；
- Candidate 已按可判断的 proposal 分组，重要替代、依赖或兼容关系已可见；
- material Compatibility Surface 已按需识别，相关 Boundary、Basis 与未决影响已可见；没有 material Surface 时未生成兼容噪音；
- 相关的边界和区分标准已可见，如存在会改变当前方向的 pressure point 也已暴露；
- 会改变 goal、scope、contract、risk 或 authority 的 open decision 已标记为 `Human Decision Required`；普通 means-level Candidate 没有被误写成 Human 已接受的决定；
- Agent candidate 没有被静默晋升为项目事实、系统承诺或执行授权。

## Plan

### 职责

吸收与执行相关的 context 和 Decision Space，从当前 Plan request 重新建立 delegation，在该边界内关闭设计与实施 choice，形成单一、一致、可执行、可验证的 Execution Model。

Plan 负责 decision closure，但不从 Shape 继承 authorization。Human 显式发起 Plan，默认委托 Agent 关闭已确认目标、scope、constraint 和风险边界内未被排除的 means-level choice；不得用实现便利重新定义领域规则、产品语义、scope、external contract 或风险接受。

Plan 的输入可以来自当前 user prompt、对话历史、shape、review 和项目现状。它不要求前序 task 或正式文档必须存在。

### Context 归纳

Plan 在内部从当前 conversation 形成与执行相关的 planning basis：

```text
Goal
Domain Rules / Invariants
In Scope / Out of Scope
Relevant Facts
Constraints
Human Decisions
Candidate Directions
Decision Criteria / Closure Requirement
Assumptions
Current Delegation
```

Plan 不机械拼接历史，也不把 Shape Candidate set 当成穷尽列表。它可以补充形成可靠方案所需的技术候选。对于相互矛盾、已经过时或被 Plan 实质重分类的 context，应明确来源和影响；具体投影规则由 [Templates README](../templates/README.md) 定义。

### Decision Closure

未被 `Rejected` 或 `Deferred` 的 Candidate，在 Human 显式发起 Plan 后默认可以被考虑。Plan 重新检查每个 material choice 是否只是完成已确认 outcome 的 means，并且没有改变 goal、产品或领域语义、scope、external contract、Compatibility Boundary、权限或重要风险接受。

满足边界的 choice 可以关闭为单一 Chosen Approach，并用 `Resolved Choice + Basis` 投影重要选择。`[Decision]` 仍仅代表 Human 明确决定；普通技术选择的 Basis 说明选择依据，只有权限来源不明显时才同时说明为什么它属于当前 delegated means，不增加固定 Authority 字段。越过上述边界的 choice 标记为 `Human Decision Required`，保留在 Human Attention 并 Handback。

### Change Surface 与 Execution Model

Plan 不只要给出步骤，还要让 Human 一眼看见当前计划实际准备修改什么：

- `Change Surface`：按 target 表达 intended change 和 reason，是 `Planned Change`，不表示现实变化或 Build authorization 已经发生；
- `Scope`：表达 Allowed Changes、Do Not Touch 和 Out of Scope，是授权边界；
- `Execution Model`：将修改面组织为有结果的 work packages、必要依赖和顺序；
- `Verification`：Success Criteria 定义 Verification Obligation，Checks 给出当前 evidence 下推荐的可执行 Verification Method；只在主要验证可能不可用或不充分时增加 Fallback 与 Residual Risk。

Change Surface 不等于文件清单，也不能被 Scope 或步骤隐式代替。每个 planned target 必须直接来自当前请求，或是完成已授权结果不可缺少的最小附带修改。无法建立这种 traceability 的相邻清理、体系对齐或“顺便完善”不得进入 Change Surface。它和公共 `Outcome` 共同承担旧 Plan 中有价值的总结与修改面确认能力，不恢复重复的 summary dashboard。

Plan 不增加 `Confirmed` 字段或审批 task。后续明确的 Build execution request 决定 Change Surface 中哪些部分成为 `Authorized Change`；发起 Build 不表示 Human 认可 Plan 的全部事实判断、理由或未被当前请求覆盖的修改面。

### Bounded Reconciliation

Plan 继承公共 Bounded Reconciliation，但它的 resolution depth 是“在 current delegation 内关闭实施 decision”。它可以自主比较并选择实施策略，调整技术约束下的方式，重排步骤、依赖与验证，并对局部事实进行有界检查。未被排除的 means-level Candidate 可以根据已确认标准关闭。

Plan 可以在既定边界内自主作出架构、模块切分、实施顺序、测试层级和验证方式等决定。Checks 默认是 Build 可根据 Actual Environment 替换的推荐方法；只有 Human 明确将特定 platform、runner、command 或 environment 纳入 acceptance / Constraint 时才固定。如果冲突会改变目标、领域规则、产品语义、scope、external contract、权限或重要风险承诺，则保留为 `Human Decision Required` 并 Handback，不用实现便利静默关闭。

Plan 可以在已建立的 Compatibility Boundary 内自主选择 Compatibility Mechanism，但不能自行创建、放宽或取消 Boundary。若 Planned Change 明显改变已知 external contract 且 Boundary 尚未建立，Plan 应保留已形成的局部 Execution Model，以 `partial` 返回并要求 Human 决定；未知或推测性的消费者不触发开放式调查。

如果 reconciliation 改变 Chosen Approach、Change Surface、Execution Model、Scope 或 Verification，Plan projection 必须按公共 material disclosure 返回 working basis、effect 与 residual。Plan 降级、排除、重分类或实质改写 Shape Candidate / Criteria / Compatibility Boundary，以及对 user request 采用会改变 inferred Change Surface 的解释，也属于 material reconciliation。

### 允许的内部操作

- 汇总并规范化实施相关 context；
- 对规划依赖的 repo 事实、依赖、工程惯例和 verification entrypoint 进行最小充分检查；
- 比较候选路径，并在委托边界内关闭设计和技术 decision；
- 检查每个 planned target 对当前请求的 traceability，明确 Change Surface，并设计 work packages、依赖、验证和必要失败处理；
- 将已建立的 Compatibility Boundary 作为 Planning Basis 吸收，并在其范围内关闭 Compatibility Mechanism；
- 在多轮 plan 中吸收新的 execution context。

### 边界

Plan 不负责：

- 重新定义 Human 的目标；
- 静默解决产品语义、external contract 或重大 scope 冲突；
- 把 Shape 中未承诺的 Candidate 当成 Human Decision；
- 重新执行无边界的 diagnosis；
- 修改被规划对象；
- 生成跨对话 handoff、routing、persistence 或默认兼容政策；
- 为未知或推测性的消费者展开开放式 compatibility archaeology；
- 因方案已经 ready 而自动进入 Build。

### 完成条件

Plan 已经足以让现代 Agent 在 build 内自主执行和验证：

- Target Outcome 与 Chosen Approach 一致；
- 主要修改面已在 Change Surface 中完整可见；
- current delegation 内需要关闭的 choice 已有 Resolved Choice 和关键 Basis，需要 Human 决定的例外已可见；
- 每个 Change Surface target 都能追溯到当前请求或不可缺少的最小附带修改；
- Change Surface、Execution Model、Scope 和 Verification 没有实质冲突；
- work packages、依赖和顺序足以让 Build 在授权内自主执行；
- Success Criteria 已定义目标的 Verification Obligation，推荐 Checks 具有可执行依据；特定方法只有在确属 acceptance / Constraint 时才被固定，不充分的验证已披露 Fallback 和 Residual Risk；
- Planned Change 涉及 material Compatibility Surface 时，Boundary 已建立、Mechanism 与其一致，并且 Verification 能检查兼容效果；
- material risk、Stop Condition、reconciliation 和 `Human Decision Required` 已可见。

## Build

### 职责

在 user prompt 的实际授权范围内实施 Authorized Change，使现实朝 Requested Outcome 发生 Actual Change，提供与风险和 claim 相称的验证证据，并以 Loop Closure Observation 结束当前 delivery loop、把控制权返回 Human。

Build 不要求正式 plan 已经存在。对于目标明确、scope 有界的工作，Agent 可以直接在 build 内形成局部策略并实施。

Build 必须区分 `Requested Outcome`、`Planned Change`、`Authorized Change` 与 `Actual Change`。Requested Outcome 是 Human 希望本轮实现的结果，Authorized Change 是当前允许 Agent 实施的现实干预，Actual Change 是现实中真正发生的变化。Plan 的 Change Surface 只是 planning context；当前 Build execution request 明确引用或在同一 conversation 中无歧义地延续唯一 current Plan 时，相关 Change Surface 才能在当前 scope、permission 和 risk boundary 内成为执行授权。当前 prompt 的缩小、排除或修正始终优先。

```text
Actual Change exists
≠ Requested Outcome achieved
≠ Target is correct or fit
```

Build completion 不形成产品整体正确性、target fitness 或 Shape / Plan 质量 verdict，除非 Requested Outcome 明确包含对应的可观察标准，且 Verification 足以支持该范围的结论。

```text
Authorized Change
= current Build request
∩ unambiguously referenced current Plan
∩ current scope / permission / risk constraints
```

task 名称、Plan 的完成状态、Human 没有反对、Memory 或 Lens 都不能单独扩大 execution authority。Build request 授权的是所描述的现实变化，不表示 Human 认可 Plan 的全部 evidence、reasoning 或未被引用的修改面。当前 prompt 的缩小、排除和修正优先；direct Build 可以从当前 request 直接建立 Authorized Change。

### 允许的内部操作

- 搜索、阅读并理解相关对象；
- 检查当前工作树并保留无关的已有修改；
- 复现问题并完成必要 diagnosis；
- 创建或修改 persistent test、fixture、instrumentation、diagnostic command、reproduction harness 或复现配置；
- 形成或调整局部实施策略；
- 修改授权范围内的代码、配置、文档或其他对象；
- 运行测试、检查和验证；
- 根据验证结果进行局部修正；
- 从项目脚本、manifest、Makefile、CI、项目文档或 repo fact 选择有来源的 verification command；
- 汇总实际变化、证据、剩余风险与当前 loop 的 material state delta。

这些操作构成 build 内部的 Agent micro ReAct loop，不需要 Human 分别路由到 review、shape 或 plan。Diagnostic artifact 可以是完整 Build outcome；“建立最小复现，不修 implementation”可以在 artifact 可运行、目标 observation 出现且失败来自预期 discriminating condition 时 `complete`。该授权不包含 implementation repair，test failure 也只支持对应 instrument、input、environment 和 dependency state 下的 observation；没有 expected behavior 或 baseline 时不得升级为 defect、root cause 或 regression。

### Reality Interaction Discipline

Build 根据 Reality feedback 运行以下内部循环，不输出固定步骤清单：

```text
Ground Requested Outcome, Authorized Change, and Actual Environment
→ identify Verification Obligation
→ choose minimum sufficient intervention and evidence method
→ act and observe
→ adapt method from evidence
→ verify claim and material effects
→ distill reusable resolution when triggered
→ Handback
```

存在实质风险差异时，Build 优先 local、reversible、isolated、staged 和 observable 的 intervention，但不生成固定 preflight 或 rollback checklist。Agent 只能自主替换 effect profile 实质等价的手段；转向 remote 或管理员权限、扩大 blast radius，或者新增通知、发布、数据写入、安全策略变化及其他 external effect 时，必须 Handback。

Plan 的 Success Criteria 是 Verification Obligation，Checks 默认是推荐方法。Build 使用项目 manifest、wrapper、script、lockfile、Makefile、CI、当前 OS / shell 和 repo evidence 形成实际 Verification Method。Shell syntax、path 和 runner 的等价调整由 Agent 自主完成；替代 evidence 较弱时必须披露 coverage gap，并据此校准 `complete / partial` 与 Remaining Risk。

Capability problem 优先通过当前权限内的非特权替代解决；需要管理员权限、凭据、新网络权限、机器级配置或安全策略变化时不能从技术需要推导授权。新的尝试只有具有 evidence-backed reason 和预期信息增益时才继续；反馈缺失、延迟、矛盾或尝试只是在排列命令时停止，不使用固定 retry budget。

### Bounded Reconciliation

Build 继承公共 Bounded Reconciliation，可以自主吸收当前授权内的文件位置、局部 API、实施策略、步骤、测试工具和运行条件偏差，并根据验证结果修正局部路径。这种 means-level autonomy 必须保持 requested outcome、authorized scope、已接受风险、effect profile 和已建立 Compatibility Boundary；只有当前 Build authorization 明确覆盖时，externally observable contract 才能按该 Boundary 改变。Verification failure 可以触发授权内局部修正，但不能创建新授权。Reality feedback 推翻 goal、scope、external contract、Compatibility Boundary、risk 或 authorization basis 时，必须披露并 Handback。

如果协调产生实质 implementation deviation，Build 结果必须同时在 Actual Changes / Incomplete / Deviated 语义中说明真实变化，并按公共 Reconciliation 结构暴露冲突、working basis、effect 与 residual。如果需要改变目标、未授权的 external contract、Compatibility Boundary、scope、权限或重要风险，必须 Handback，不能把冲突、验证失败或新 evidence 当成扩大 Build 授权的理由。

Command 的 cwd、runner 与来源属于内部 grounding。环境失败应通过 repo evidence 选择有依据的修正；等价方法可以自主采用，较弱 fallback 必须披露覆盖边界。`Verification: blocked` 不机械决定 task Status：已有有用 Actual Change 时通常为 `partial`，充分替代 evidence 可以支持 `complete`，没有有用现实结果且关键验证无法进行时才为 `blocked`。完整规则见 [Build task](build.md)。

### 实施收尾

Build 在 Handback 前必须区分四类内容：

1. `Actual Changes`：现实中已经发生了什么；
2. `Incomplete / Deviated`：哪些未完成、跳过、阻塞或偏离原策略；
3. `Loop Closure Observation`：当前 loop 从 Starting Gap 到 Reality 的 material delta，以及仍存在的 Remaining Gap；
4. `Reusable Insight`：本轮是否产生了将来可能复用的新经验。

Actual Changes 与相关 Incomplete / Deviated 属于执行核对。预期且持久的 effect 进入 Actual Changes，已经发生的 material unintended effect 进入 Incomplete / Deviated，无法完全观察、验证或清理的 effect 进入 Remaining Risk；撤销 repo diff 不会自动消除 generated artifact、cache、watcher、hook、external state 或 temporary resource。Routine、可逆且无残留的试验无需投影。

每次 Build 在语义上都形成 Loop Closure Observation；简单 direct Build 已由 Outcome、Actual Changes 和 Verification 完成闭合时不重复独立 section，material Review / Shape / Plan 演化才压缩 Starting Gap、Material Shift 与 Remaining Gap。该 closure 只在当前 evidence 下成立。Material Shift 必须来自可观察 Human Decision、Constraint、system evidence 或已披露 Reconciliation；Build 不能通过事后叙事重写前序 context。

Delayed effect 属于 Requested Outcome acceptance 时，`complete` 需要直接 observation 或足以支持该 claim 的有效 proxy，否则返回 `partial`。如果 delayed effect 在当前 acceptance 外但仍是可能后果，可以在其余条件成立时 `complete`，并在 Remaining Risk 中注明 observation window 和证据边界。

Reusable Insight 通常省略；但成功解决的 operational friction 同时满足 material、可能复发、已有成功 evidence、适用边界可说明时必须投影。Reusable resolution 压缩 Trigger、Working Resolution、Evidence 和 Applicability Boundary；失败路径只有能防止未来重复昂贵、越权或无信息增益的尝试时才保留，不记录完整试错历史。本轮新发现默认是 heuristic 或 lesson；只有独立项目规范、Human-confirmed constraint，或修改前已经充分建立的 evidence 才能支持 invariant。本轮新写入的代码或测试不能成为新 invariant 的唯一证明。

Reusable Insight 不会自动更新项目文档、Memory 或 Project Lens。Human 可以在后续重复验证后另行决定是否沉淀；当前 task 不执行知识晋升。

### 边界

Build 必须 Handback，当工作需要：

- 改变目标或关键产品语义；
- 显著扩大 scope；
- 获得新的权限或承担新的重要风险；
- 使用需要管理员权限、凭据、新网络访问、机器级配置、安全策略变化或不同 material effect profile 的执行 / 验证方法；
- 在会实质改变结果的候选方向之间作出 Human 决策；
- 执行未被明确授权的不可逆操作。

Build 不因 task 名称获得无限写权限，也不在完成后自动进入 Orient、Review 或下一次 Build。

Existing dirty worktree 属于 repo reality。Build 保留无关修改，不用 reset、checkout、顺手重写或格式化清理 Human 的工作；当前任务与已有变化实质重叠且无法安全区分时必须 Handback。

### 完成条件

- Requested Outcome 已通过 Authorized Change 实现，或明确说明无法完成的边界；
- 实际变化被清楚列出；
- 完成了与风险和 claim 相称的验证，且 Actual Change、Requested Outcome achievement 与 target fitness 没有被混为同一结论；
- diagnostic artifact、material side effect、清理残留和 delayed-feedback boundary 已按需验证并披露；
- Verification Method 已根据 Actual Environment 有依据地适配，没有静默降低 Verification Obligation；
- 不确定性、偏差和未完成事项已经披露；
- 当前 delivery loop 已形成 Loop Closure Observation；简单结果没有重复总结，material state delta 已按需可见；
- material、可能复发且已成功解决的 operational friction 已提炼为带 trigger、resolution、evidence 和 boundary 的 lesson / heuristic；其他情况没有制造经验，本轮新代码或测试也没有自证新的 invariant；
- 控制权已经 Handback 给 Human。

`Status: complete` 只表示 Requested Outcome 已通过 Authorized Change 实现，并获得与风险和 claim 相称的 Verification；它不表示 target 整体正确、适用或整个项目结束。明确位于当前 scope 外的 Remaining Gap 可以成为未来新 loop 的输入，但不会自动触发下一 task。

## Shared Autonomy And Handback

如果当前 task 内缺少的信息可以在既定目标、scope 和权限内安全补足，Agent 应自行完成必要的局部观察、搜索、diagnosis、规划或验证。出现以下情况时必须 Handback：

- 需要改变 Human 目标或关键产品语义；
- 需要显著扩大 scope；
- 需要新的权限、风险承诺或不可逆操作授权；
- 存在会实质改变结果、且上下文无法确定的 Human decision；
- 当前 task 的预期结果与 user prompt 无法安全调和。

Task 之间的反馈关系、同-task 多轮收敛和宏观 ReAct 对应统一见 [Loop](../loop.md)，不在本文重复定义。
