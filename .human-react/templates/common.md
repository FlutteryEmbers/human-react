# Common Chat Projection

本文件是 Human ReAct 公共 chat projection contract 的唯一规范源。它定义所有 task 共享的结果头部、状态、语义边界与 material disclosure；每个 task 的 Context 结构由对应的 task-specific template 定义。

```text
Task Prompt
+ Common Projection
+ Task-specific Projection
→ Complete Task Contract
```

Template 只组织可观察结果，不定义 task procedure、工具权限、持久状态或自动路由。Task 的职责和行为边界优先于 projection。

## Task Result

```markdown
## Task Result

- Task: orient | review | shape | plan | build
- Status: complete | partial | blocked
- Outcome: <本轮最重要的结果>
- Human Attention: <需要 Human 决定、授权或关注的风险；没有则为 none>

### Context

<由 task-specific template 组织>
```

公共头部应让 Human 在第一屏内识别结果、完成状态和是否需要介入。`Outcome` 必须直接表达 semantic result，不能只报告“已分析”“已完成”或工具执行过程。

不存在公共 `Continuation`、`Candidate Task` 或 handoff packet。Task-specific template 可以按需给出少量后续可能性，但它们不是路由、默认下一步或新授权。

## Status

`Status` 只描述当前请求的完成状态，不评价下一 task readiness，也不构成执行授权：

- `complete`：当前 task 的预期结果已经形成；
- `partial`：已经形成有用结果，但存在明确未完成部分；
- `blocked`：缺少 Human decision、权限或必要证据，无法形成当前预期结果。

Task-specific template 可以收紧这些定义，但不能改变三种状态的公共含义。`blocked` 不等于 target 本身不合格，也不等于 Review finding 的 `[Blocking]`。

## Request Alignment

Agent 在开始工作前必须识别 Human 当前实际请求，并在执行中检查结果是否偏离。该行为默认不增加公共 `User Intent` 字段。

只有在下列情况下，才在 Context 中按 task 语义投影 Request、Target、Question 或相应替代名称：

- 请求复杂、多部分或存在歧义；
- 结果为 `partial` 或 `blocked`，需要说明已覆盖与未覆盖的部分；
- 实际结果与原请求发生 material deviation；
- Requested Outcome、scope 或授权边界容易混淆。

Agent 推断不能写成 Human intent 或授权；无法直接确认的事实前提使用 `[Assumption]`，只分析“如果成立”使用 `[Conditional]`。

## Context Semantics

Context 是当前 task 新产生的 working view。Agent 可以依赖当前 conversation 已经存在的 request、事实与决定；结果不要求脱离原 chat 后仍然自包含。

需要区分认识或承诺类型时，可以按需使用：

```text
[Fact]          已确认事实
[Evidence]      可观察依据
[Diagnosis]     有证据支持的原因判断
[Decision]      Human 明确决定
[Constraint]    必须遵守的边界
[Assumption]    为继续工作暂时采用、尚未证实的前提
[Conditional]   不接受前提，只表达“如果成立会怎样”
[Candidate]     能作为整体被接受、拒绝或比较的未承诺 proposal
[Deferred]      已识别但当前明确不吸收的内容
[Open]          未解决的问题或选择
[Risk]          影响 Human 判断或后续行动的风险
[Change]        已发生的状态变化
[Verification]  已执行或要求的验证
[Invariant]     已由项目证据确认的稳定约束
[Lesson]        本轮证据支持、可能复用的认识
[Heuristic]     尚只由有限案例支持的调查或实施提示
```

标签不是必填字段，也不构成独立 section。未确认状态不能仅因进入 projection 就晋升为 Fact、Decision、Constraint 或授权。Review-specific finding classification 和 Build-specific reusable semantics 由各自 template 定义。

## Commitment Projection

所有 projection 必须保持以下状态分离：

```text
Candidate consideration ≠ Human Decision
Resolved Choice ≠ Build Authorization
Planned Change ≠ Authorized Change ≠ Actual Change
```

- Shape 不把 Candidate 可供 Plan 考虑表达为 Human permission；
- Plan 的 Change Surface 是 Planned Change，不是现实变化或 Build authorization；
- Build request 只授权其明确引用或在上下文中无歧义延续、且仍位于当前 scope、permission 和 risk boundary 内的修改；
- Human 发起 Build 不表示认可 Plan 的全部事实判断或 reasoning。

这些语义不增加公共必填 section。只有 authority 不明显或发生 material boundary event 时才展开 Basis、Reconciliation 或 Human Attention。

## Compatibility by Exception

Compatibility 只在 material Surface 存在，或 preserve / break 会实质改变 contract、scope、成本、风险或 acceptance 时投影：

```text
No established Compatibility Obligation
≠ preserve required
≠ breaking authorized
```

没有 material Surface 时省略整个 compatibility trace，不生成空占位。Surface 未知时保持 Unknown，不推断不存在消费者，也不为推测性消费者生成兼容工作。具体 Boundary 与 Mechanism 由 Shape、Plan、Build 和 Review 的 task-specific template 按各自职责表达。

## Material Reconciliation Disclosure

Task 按 [Bounded Reconciliation](../tasks/README.md#bounded-reconciliation) 在内部协调 context 冲突。只有协调实质改变当前结果，或不披露会让 Human 误解 working basis 时，才使用：

```markdown
### Reconciliation

- Conflict: <什么内容不一致>
- Resolution: resolved | provisional | preserved | unresolved
- Working Basis: <本轮如何继续>
- Basis: <关键可观察依据>
- Effect: <如何影响当前 task 结果>
- Residual: <仍未解决的部分>
```

以下情况通常需要投影：

- Agent 降级、排除或重新解释了 Human 明示 context；
- working basis 实质改变 Outcome、Finding、Decision Space、Execution Model、Actual Change 或授权边界；
- Resolution 为 `provisional`、`preserved` 或 `unresolved`，且会影响 Human 判断；
- 冲突影响 task-specific classification、deviation、verification coverage 或 residual risk；
- 不披露会让 Human 误以为冲突已被完全解决。

无内容字段与整个无关 section 直接省略。Routine conflict、工具尝试、调查时间线和隐藏推理不进入 Reconciliation。它不替代 `[Conditional]`、`[Candidate]`、`[Risk]`、Human Attention 或 task-specific deviation。

Reconciliation state 不机械决定 Status。已形成预期结果或可靠结论边界时仍可 `complete`；有用结果未完整时为 `partial`；无法形成任何有用结果时才为 `blocked`。

## Closure Projection

Closure Check 的内部行为由 [Tasks README](../tasks/README.md#task-closure) 定义。Projection 只返回对 Human 判断有价值的 closure state：

- 已完成且有支持的内容用于校准 Status 与 Outcome；
- 未完成、跳过、偏离、风险或 `Human Decision Required` 按 task-specific 结构投影；
- 没有信息价值时不输出公开 checklist、self-audit badge 或必填 Reflection；
- Task Result 中的经验不会自动写入项目文档、Memory 或 Lens；
- 完成当前 task 不会自动启动下一 task。

Build 的 Actual Changes、Verification、Loop Closure 和 Reusable Insight 规则只由 Build projection 定义，不在公共契约中重复。

## Projection Rules

- 结果优先，使用 stable Markdown headings、English field keys 和用户语言内容；
- 使用明确 target、路径、对象和行为，避免含糊代词；
- 使用 progressive disclosure，只展开有判断价值的证据和细节；
- 普通 `complete` 结果保持简短，异常结果按需展开；
- 将当前 conversation 视为 working context，只投影本轮新增且有价值的信息；
- 省略无关 section，不为填满格式制造内容；
- 不输出工具流水账或隐藏 chain-of-thought；
- 不把 Agent 建议、推断或 template 内容升级为授权；
- 不输出 readiness/confidence 数值、形式化 dashboard 或自动 next-task；
- 没有变化时直接表达 no change；没有 Human action 时使用 `Human Attention: none`。

Template 不得保存 session、memory、artifact ID 或 source-of-truth，不得引入 persist、sync、archive、自动文档更新、task routing 或跨 Agent handoff。对话中的 Task Result 是当前 working view，不是必须同步的正式文档。
