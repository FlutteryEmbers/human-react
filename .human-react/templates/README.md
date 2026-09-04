# Chat Projection Templates

本目录定义 Human ReAct task 结果如何投影到 chat：

- [`orient`](orient.md)
- [`review`](review.md)
- [`shape`](shape.md)
- [`plan`](plan.md)
- [`build`](build.md)

Template 是 projection contract，不是 task procedure、持久化 artifact 或运行状态。输出首先帮助 Human 快速抓住重点，并在当前 conversation 中留下必要的状态变化。

`orient`、`review`、`shape`、`plan` 与 `build` 均已有第一版 projection。本文件定义共同骨架与设计准则。

## Core Model

Task result 是 Human observation 和 conversation checkpoint，不是需要搬运完整上下文的 handoff packet：

```text
Semantic Result
        ↓
Human-readable Conversation Checkpoint
```

## Common Skeleton

```markdown
## Task Result

- Task: orient | review | shape | plan | build
- Status: complete | partial | blocked
- Outcome: 本轮最重要的结果
- Human Attention: Human 需要决定、授权或关注的风险；没有则为 none

### Context

<!-- 当前 task 新产生且对 Human 判断有价值的内容。 -->
```

公共头部保持简短。`Context` 的结构由 task-specific template 定义。

不存在强制的公共 `Continuation` 或 `Candidate Task`。未解决事项只有在确实需要 Human 介入时才进入 `Human Attention`，否则由 task-specific `Context` 按需表达。Task-specific template 可以投影少量、证据驱动的后续选项，但它们不得成为自动路由或新授权。

`Status` 只描述当前请求的完成状态，不评价下一 task readiness，也不构成执行授权：

- `complete`：当前 task 的预期结果已经形成；
- `partial`：已经形成有用结果，但存在明确未完成部分；
- `blocked`：缺少 Human decision、权限或必要证据，无法形成当前预期结果。

## Request Alignment

Agent 在开始工作前必须识别 Human 当前实际请求，并在执行中用它检查结果是否偏离。这是 task 内部的 alignment 行为，不是公共输出必须重复的字段；公共骨架不增加 `User Intent`。

只有在下列情况下，才应在 `Context` 中按需投影 `Request`、`Target` 或 `Question`：

- 请求复杂、多部分或存在歧义；
- 结果为 `partial` 或 `blocked`，需要说明已经覆盖和未覆盖的部分；
- 实际结果与原请求发生偏离；
- `build` 的 requested outcome、scope 或授权边界容易混淆。

优先使用与 task 语义一致的名称：Orient 使用 `Learning Focus` / `Understanding Model` / `Relevant Background` / `Alternative Perspectives` / `Understanding Boundary`，Review 使用 `Target` / `Question`，Shape 使用 `Human Anchor` / `Current Take` / `Decision Space` / `Model Delta`，Plan 使用 `Target Outcome` / `Planning Basis` / `Chosen Approach` / `Change Surface` / `Execution Model`，Build 使用 `Requested Outcome` / `Authorized Change` / `Actual Change`。不要把 Agent 推断写成 Human intent 或授权；无法直接确认的内容必须标为 `[Assumption]`。Plan 的每个 Change Surface target 还必须能追溯到当前请求或不可缺少的最小附带修改。

## Context

Context 是当前 task 新产生的 working view。Agent 可以直接使用当前 conversation 中已经存在的 request、intent、事实和决策；不要求每份结果脱离原 chat 后仍然自包含。

需要区分语义类型时，可以按需使用：

```text
[Fact]          已确认事实
[Evidence]      可观察依据
[Diagnosis]     有证据支持的原因判断
[Decision]      Human 明确决定
[Constraint]    必须遵守的边界
[Assumption]    尚未证实的前提
[Conditional]   不接受某个未确认前提，只投影“若成立会如何”
[Candidate]     Human 或 Agent 提出、能作为整体被接受、拒绝或比较的最小未承诺 proposal
[Deferred]      已识别但当前明确不吸收的内容
[Open]          未解决的问题或选择
[Risk]          影响 Human 判断或后续执行的风险
[Change]        已发生的状态变化
[Verification]  已执行或要求的验证
[Invariant]     已由项目证据确认的稳定约束
[Lesson]        本轮证据支持、可能复用的认识
[Heuristic]     尚只由有限案例支持的调查或实施提示
```

标签不是必填字段。对于当前连续对话中的短轮次 Shape，默认只投影一个 primary focus 与有决策价值的 model delta、candidate、pressure point 或 open decision。本轮更新成功即可为 `complete`，不因对话未结束而机械标记 `partial`。只有 Human 要求总结、对话变长、重要旧表述相互冲突或即将进入高风险执行时，才需要 consolidated decision space。

`[Assumption]` 表示 task 为继续工作而暂时采用的前提；`[Conditional]` 只表示依赖该前提的方向性推演，不将前提或结果升级为已确认状态。它是可选语义标签，不增加公共必填 section。

Candidate 的边界由 decision granularity 决定，不由段落数量或“是否已证实”决定。需要一起接受的组成部分归入同一 Candidate；只有可独立决定、相互替代或需要分别确认时才拆分。互斥候选使用 Pressure Point 和适用的 Decision Criteria；依赖或兼容关系按需用自然语言表达，不增加公共 relationship schema。

Candidate 默认是当前有用但未承诺、非穷尽的 proposal。未被 `Rejected` 或 `Deferred` 的普通 means-level Candidate，在 Human 显式发起 Plan 后可以被考虑和关闭，不需要逐项 `Owner`、`Delegable` 或 approval 字段。会改变 goal、value、scope、external contract、risk acceptance 或 authority 的例外选择才使用 `Human Decision Required` 并进入 Human Attention。

## Commitment Projection

输出必须保持以下状态分离：

```text
Candidate consideration ≠ Human Decision
Resolved Choice ≠ Build Authorization
Planned Change ≠ Authorized Change ≠ Actual Change
```

- Shape 不把 Plan eligibility 表达成 Human permission；
- Plan 的 Change Surface 是 Planned Change，不是现实变化或 Build authorization；
- Build request 只授权其明确引用或在上下文中无歧义延续、且仍位于当前 scope / permission / risk boundary 内的修改；
- Human 发起 Build 不表示认可 Plan 的全部事实判断或 reasoning。

这些语义不增加公共 section。普通结果只使用现有 Candidate、Decision、Resolved Choice、Change Surface、Actual Changes、Basis、Reconciliation 和 Human Attention；只有 authority 不明显或存在 material boundary event 时才展开说明。

## Compatibility by Exception Projection

Compatibility 只在 material Surface 存在，或 preserve / break 会实质改变 contract、scope、成本、风险或 acceptance 时投影。它不增加公共头部、固定 section 或 schema：Shape 使用现有 `[Fact]`、`[Constraint]`、`[Decision]`、`[Candidate]`、`[Open]` 和 Basis 表达 Surface 与 Boundary；Plan 使用 Planning Basis、`Resolved Choice + Basis`、Verification、Reconciliation 与 Human Attention 表达已建立 Boundary 下的 Mechanism 和未决边界。

```text
No established Compatibility Obligation
≠ preserve required
≠ breaking authorized
```

没有 material Surface 时省略整个 compatibility trace，不生成空兼容占位。Surface 未知时保持 Unknown，不推断不存在消费者，也不为推测性消费者生成兼容工作。Plan 对 Shape Boundary 的降级、改写或冲突按 material reconciliation disclosure 返回；Compatibility Mechanism 不会反向创建或改变 Boundary。

## Material Reconciliation Disclosure

Task 按 [Bounded Reconciliation](../tasks/README.md) 在内部自主协调 context 冲突。只有协调实质改变当前结果或不披露会让 Human 误解结论依据时，才在 `Context` 中使用以下可选结构：

```markdown
### Reconciliation

- Conflict: <什么内容不一致>
- Resolution: resolved | provisional | preserved | unresolved
- Working Basis: <Agent 本轮如何继续>
- Basis: <支持该处理的关键可观察依据>
- Effect: <如何影响当前 task 结果>
- Residual: <仍未解决的部分>
```

在以下情况中应投影：

- Agent 降级、排除或重新解释了 Human 明示 context；
- Agent 对 requested target、scope 或 delegation 采用了会改变 inferred Change Surface 的解释；
- Plan 降级、排除、重分类或实质改写了 Shape Candidate / Criteria；
- Plan 降级、改写或采用了与 Shape / Human context 冲突的 Compatibility Boundary；
- working basis 实质改变 Outcome、Finding、Decision Space、Execution Model 或 Actual Change；
- Resolution 为 `provisional`、`preserved` 或 `unresolved`，且该状态会影响 Human 判断；
- 冲突影响 Use Verdict、finding classification、授权边界、implementation deviation 或 residual risk；
- 不披露会使 Human 误以为输入 context 被直接接受或某个冲突已被完全解决。

无内容的字段和整个无关 section 直接省略。Routine conflict、工具尝试、调查时间线和隐藏推理不进入该 section。Reconciliation 不替代 `[Conditional]`、`[Candidate]`、Review finding classification、`[Risk]`、`Human Attention` 或 task-specific deviation；它只说明 Agent 如何处理会影响结果的冲突。

`provisional`、`preserved` 或 `unresolved` 本身不决定 `Status`。如果 task 已形成预期结果或可靠地建立了结论边界，仍可为 `complete`；有用结果未完整时为 `partial`；无法形成任何有用结果时才为 `blocked`。

## Inherited Output Semantics

Human ReAct 只继承 Workflow Lite 输出中直接帮助 Human 判断和当前 conversation 继续工作的语义，不继承其 dashboard、routing、handoff 或 persistence 结构。

| Task | Context 应能表达 | 继承理由 |
| --- | --- | --- |
| `orient` | Learning Focus、Understanding Model、直接相关的 background、materially different perspective，以及必要的 target/general/interpretation/unknown boundary | Orient 帮助 Human 建立理解，但不把通用知识或解释性综合伪装成当前 target 的事实 |
| `review` | target/question、evidence、findings、gap/diagnosis、uncertainty、Human decision，以及适用时的 intended-use verdict 和 disposition classification | 重要判断必须能回到明确 target 和 evidence；Blocking 必须指向具体 intended use，未知不能伪装成 verdict |
| `shape` | 必要时的 Human Anchor、可修正的 Current Take、evidence-backed facts、normative constraints/decisions、candidate/conditional directions、decision criteria、model delta、pressure point、material Compatibility Boundary 与 `Human Decision Required` | 显式暴露从 Human 表达到系统语义的翻译；普通 means-level Candidate 不需要逐项 approval，兼容信息只按 material trigger 出现 |
| `plan` | target outcome、chosen approach、resolved choices、planned change surface、execution model、必要 scope/do-not-touch、Verification Obligation、推荐 Checks、risks、stop conditions、已建立 Boundary 下的 Compatibility Mechanism 与需要 Human 决定的 gaps | Outcome 和 Change Surface 帮助 Human检查路径与计划修改面；Plan 定义要证明什么，但不把推荐方法误作 Build 必须原样执行的命令 |
| `build` | requested outcome 的实现状态、actual changes、实际 verification method 与 coverage、incomplete/deviated、remaining risk、必要时的 loop closure，以及触发时的 reusable resolution | Build 必须区分允许的现实干预、实际变化与结果达成，并以 evidence-bounded reality 与 material state delta 关闭当前 delivery loop；material operational resolution 必须带证据和适用边界 |

这些是 task-specific template 应按当前实现覆盖的 semantic content，不是公共骨架的固定字段。只在当前结果有相关内容时投影。

共同继承五条输出约束：

1. Orient 直接回答 Learning Question，只投影对理解有用的背景和视角，并在可能混淆时区分 target-specific fact、general model、interpretation 与 unknown；
2. Review 的重要判断必须有 evidence；它可以用 Review-specific 的 Blocking / Material / Minor / Validated 帮助扫描，但不得以 classification 替代 evidence、gap、diagnosis 或 risk；
3. Shape 必须区分 Human Anchor、Current Take、evidence-backed fact、normative constraint/decision、Candidate 和 Conditional；Candidate 按 decision unit 分组，默认只投影 focused model delta；
4. Plan 必须用 Outcome 总结路径，用 Change Surface 显示 Planned Change，区分 Human `[Decision]` 与 Agent `Resolved Choice`，并用 Success Criteria 定义 Verification Obligation；Checks 默认是 Build 可按 Actual Environment 调整的推荐方法；
5. Build 以 requested outcome、actual changes 和 verification 为中心；Actual Change 不自动证明 Requested Outcome 达成或 target fitness。Build 必须披露相关 incomplete/deviated、material residual effect 与 remaining risk；每次 Build 在语义上形成 evidence-bounded Loop Closure Observation，只有 material multi-task 演化才增加独立 Loop Closure；满足 operational-resolution trigger 时必须投影 scoped Reusable Insight。

Repo-fit 与 completeness check 等自检保留为 task 内部行为。只有自检失败、暴露不确定性或需要 Human 介入时，才通过 `Status`、`Human Attention` 或 `Context` 投影；不输出 self-audit badge、readiness score 或完整 checklist。

Blocking / Material / Minor / Validated 是 Review-specific disposition，不进入上述公共语义标签，也不自动推广到 Shape、Plan 或 Build。Review 不生成平行的 Severity、Blocking Gaps、Non-blocking Gaps、Confidence 或 Readiness dashboard。

## Closure Projection

Closure Check 的内部行为由 [Tasks README](../tasks/README.md) 定义。Template 只决定哪些 closure 结果值得投影：已完成和已验证的内容用于校准 `Status` 与 `Outcome`；未完成、跳过、偏离、风险或 `Human Decision Required` 按需进入 `Human Attention` 与 task-specific `Context`。不输出公开 checklist 或必填 `Reflection` 字段。

对 Build 而言，actual changes、未完成事项和 deviation 属于基本执行核对，不能藏在“反思”中。可复用经验应与它们分开，并遵循：

- 通用套话、工具流水账和对 `Outcome` 的复述不算经验；
- 本轮新发现优先标为 `[Heuristic]` 或 `[Lesson]`，不得伪装成 project invariant；
- 只有独立项目规范、Human-confirmed constraint，或修改前已由充分 evidence 建立的稳定规则才标为 `[Invariant]`；本轮新写入的代码或测试不能成为新 Invariant 的唯一证明；
- 成功解决的 operational friction 同时满足 material、可能复发、已有成功 evidence、适用边界可说明时，使用一条 Lesson / Heuristic 压缩 Trigger、Working Resolution、Evidence 和 Applicability Boundary；
- 其他情况下省略相关内容，不输出 `Reflection: none` 或 `Lessons: none`；
- Task Result 中的经验不会自动写入项目文档、Memory 或 Lens。

Loop Closure 也不是 Reflection、summary packet 或 execution log。它只压缩与当前 Authorized Change 有直接因果关系的 Starting Gap、Material Shift 和 Remaining Gap，并且只在当前 evidence 下 provisional 成立。Material Shift 必须对应可观察 Human Decision、Constraint、system evidence 或已披露 Reconciliation，不能借事后总结重写前序 context。简单 direct Build 已由 Outcome、Actual Changes 和 Verification 完成闭合时省略独立 section；Remaining Gap 可以存在于 `complete` Build 中，但不会自动触发下一 task。

## Design Rules

- 结果优先，公共头部应在第一屏内完成快速判断；
- 使用 stable Markdown headings、English field keys 和用户语言内容；
- 使用明确 target、路径、对象和行为，避免含糊代词；
- 区分 fact、Human decision、constraint、assumption 和 open question；
- 使用 progressive disclosure，只展开有决策价值的证据和细节；
- 普通 `complete` 结果保持简短，`partial`、`blocked` 和异常结果按需展开；
- 将当前 conversation 视为 working context，只投影本轮新增且有判断价值的信息；
- 省略无关 section，不为填满格式编造内容；
- 不输出工具流水账或隐藏 chain-of-thought，只投影结果和可观察依据；
- 不把 Agent 建议、推断或 template 内容升级为授权；
- 不为满足格式而生成空泛反思或未经证据支持的“最佳实践”；
- 不输出 readiness/confidence 数值、形式化 self-audit badge 或 routing dashboard；
- 没有变化时直接表达 no change，没有 Human action 时写 `Human Attention: none`。

## Boundaries

Template 不得：

- 保存实际 chat 输出或维护 session、memory、artifact ID、source-of-truth；
- 引入 persist、sync、archive 或自动文档更新；
- 定义 task 的职责、工具权限或执行步骤；
- 扩大 user prompt 的 scope 或授权；
- 自动选择、调用或启动下一 task；
- 将单次经验自动晋升为项目规则、Memory 或 Project Lens；
- 将一次 projection 描述成唯一规范状态。

对话中的最新 Task Result 是 working view，不是必须同步的正式文档。

## Relationship With Tasks

```text
tasks/README.md
定义 task 的 responsibility、allowed operations、boundaries、completion 和 Handback。

tasks/<task>.md
承载可运行的 task-specific prompt；未实现的 task 可保持空文件。

templates/*.md
定义 semantic result 如何形成 Human-readable 的 conversation checkpoint。
```

Task 的职责和边界优先。Template 只能组织表达，不能扩张行为。

Task prompt 的公共骨架由 [Tasks README](../tasks/README.md) 定义。Task 文件不复制 Result Packet，Template 也不定义 Working Policy、Handback 或 Complete When。

设计 task-specific projection 时，只补充 `Context` 的组织方式和必要的条件 section；不引入独立 summary packet、handoff packet、强制 Candidate Task 或强制 continuation。Plan 的 Change Surface 是对实际修改面的 task-specific 投影，不是与公共 Outcome 重复的 summary dashboard。
