# Chat Projection Templates

本目录定义 Human ReAct task 结果如何投影到 chat：

- [`review`](review.md)
- [`shape`](shape.md)
- [`plan`](plan.md)
- [`build`](build.md)

Template 是 projection contract，不是 task procedure、持久化 artifact 或运行状态。输出首先帮助 Human 快速抓住重点，并在当前 conversation 中留下必要的状态变化。

`review` 已有第一版 projection；其余 task-specific template 仍为空。本文件定义共同骨架与设计准则。

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

- Task: review | shape | plan | build
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

优先使用与 task 语义一致的名称：Review 使用 `Target` / `Question`，Shape 使用 `Current Intent`，Plan 使用 `Target Outcome` / `Planning Basis`，Build 使用 `Requested Outcome` / `Authorized Scope`。不要把 Agent 推断写成 Human intent 或授权；无法直接确认的内容必须标为 `[Assumption]`。

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
[Open]          未解决的问题或选择
[Risk]          影响 Human 判断或后续执行的风险
[Change]        已发生的状态变化
[Verification]  已执行或要求的验证
[Invariant]     已由项目证据确认的稳定约束
[Lesson]        本轮证据支持、可能复用的认识
[Heuristic]     尚只由有限案例支持的调查或实施提示
```

标签不是必填字段。对于当前连续对话中的短轮次 Shape，`partial` 结果只需投影 meaningful delta、失效假设、冲突和仍影响后续行动的开放问题。Shape 收敛时可以给出简短当前结论；只有 Human 要求总结、对话变长、重要旧表述相互冲突或即将进入高风险执行时，才需要 consolidated current intent。

`[Assumption]` 表示 task 为继续工作而暂时采用的前提；`[Conditional]` 只表示依赖该前提的方向性推演，不将前提或结果升级为已确认状态。它是可选语义标签，不增加公共必填 section。

## Inherited Output Semantics

Human ReAct 只继承 Workflow Lite 输出中直接帮助 Human 判断和当前 conversation 继续工作的语义，不继承其 dashboard、routing、handoff 或 persistence 结构。

| Task | Context 应能表达 | 继承理由 |
| --- | --- | --- |
| `review` | target/question、evidence、findings、gap/diagnosis、uncertainty、Human decision | 重要判断必须能回到明确 target 和 evidence；未知不能伪装成 verdict |
| `shape` | 默认表达 meaningful delta、失效假设和 open decisions；收敛时表达简短当前结论；必要时才生成 consolidated current intent | 连续讨论直接依赖 chat，不重复序列化已有 intent |
| `plan` | chosen approach、必要 scope/do-not-touch、steps、verification、risks、stop conditions、unresolved conflicts | Plan 应足以支持当前 conversation 中的执行和验证，但不重建完整 planning basis，也不能替 Human 吸收 intent-level 冲突 |
| `build` | outcome、actual changes、verification、deviations、skipped/blocked work、remaining risk，以及有真实新内容时的 reusable insight | Build 必须说明现实发生了什么；经验只在证据支持且能改变未来行动时投影 |

这些是 task-specific template 后续需要覆盖的 semantic content，不是公共骨架的固定字段。只在当前结果有相关内容时投影。

共同继承四条输出约束：

1. Review 的重要判断必须有 evidence；
2. Shape 必须在内部吸收 current intent；默认投影 meaningful delta，只有对 Human 判断有价值时才汇总 current intent；
3. Plan 必须包含可信 verification，并在相关时给出 stop conditions；
4. Build 以 actual changes 和 verification 为中心，必须披露相关 deviation、skipped/blocked work 与 remaining risk；reusable insight 始终可选。

Preflight、input sufficiency、completeness check 等自检保留为 task 内部行为。只有自检失败、暴露不确定性或需要 Human 介入时，才通过 `Status`、`Human Attention` 或 `Context` 投影；不输出 self-audit badge、readiness score 或完整 checklist。

## Closure Projection

Closure Check 的内部行为由 [Tasks README](../tasks/README.md) 定义。Template 只决定哪些 closure 结果值得投影：已完成和已验证的内容用于校准 `Status` 与 `Outcome`；未完成、跳过、偏离、风险或 Human-owned decision 按需进入 `Human Attention` 与 task-specific `Context`。不输出公开 checklist 或必填 `Reflection` 字段。

对 Build 而言，actual changes、未完成事项和 deviation 属于基本执行核对，不能藏在“反思”中。可复用经验应与它们分开，并遵循：

- 通用套话、工具流水账和对 `Outcome` 的复述不算经验；
- 一次案例得到的提示优先标为 `[Heuristic]`，不得伪装成 project invariant；
- 只有代码、规范或足够验证支持的稳定约束才标为 `[Invariant]`；
- 没有新经验时省略相关内容，不输出 `Reflection: none` 或 `Lessons: none`；
- Task Result 中的经验不会自动写入项目文档、Memory 或 Lens。

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
未来承载可运行的 task-specific prompt。

templates/*.md
定义 semantic result 如何形成 Human-readable 的 conversation checkpoint。
```

Task 的职责和边界优先。Template 只能组织表达，不能扩张行为。

Task prompt 的公共骨架由 [Tasks README](../tasks/README.md) 定义。Task 文件不复制 Result Packet，Template 也不定义 Working Policy、Handback 或 Complete When。

设计 task-specific projection 时，只补充 `Context` 的组织方式和必要的条件 section；不引入独立 summary、handoff packet、强制 Candidate Task 或强制 continuation。
