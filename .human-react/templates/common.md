# Common Chat Projection

本文件是所有 task 共享输出契约的唯一规范源。它只组织 Human 可观察结果，不定义 task procedure、权限、持久状态或自动路由；显式 Lens 的 declared effect 只在现有 `Context` 中投影 receipt。

## Task Result

```markdown
## Task Result

- Task: orient | review | shape | plan | build
- Status: complete | partial | blocked
- Outcome: <本轮最重要的结果>
- Human Attention: <需要 Human 决定、授权或关注的风险；没有则为 none>

### Context

<仅在 Outcome 之外仍有判断价值时出现>
```

`Outcome` 直接表达 semantic result，不使用“已分析”“已完成”或工具过程代替。`Human Attention: none` 保留，帮助 Human 在第一屏判断是否需要介入；没有补充内容时省略整个 Context。

## Status

- `complete`：当前 task 的预期结果已经形成；
- `partial`：已有有用结果，但存在明确未完成部分；
- `blocked`：缺少 Human decision、权限或必要 evidence，无法形成任何有用结果。

Status 只描述当前请求的完成度，不评价下一 task readiness、target fitness 或整个项目，也不构成现实授权。Task-specific template 可以收紧含义，但不能改变这三个状态。

## Declared Effect Receipt

Human 显式选择 effectful Lens 后，不增加公共字段。成功 effect 在 `Context` 中使用：

```markdown
### Context

- Memory Capture: `.human-react/memory/captures/<document>.md`
```

声明的 effect 失败时使用 `Memory Capture: failed — <reason>`。主 task 已形成有用结果时 Status 为 `partial`；主 task 也无法形成任何有用结果时才为 `blocked`。没有选择 effectful Lens 时不输出 receipt。

## Request Alignment

Agent 默认依赖当前 conversation，不重复 User Intent。只有请求复杂或歧义、结果为 `partial / blocked`、发生 material deviation，或 target、scope、Requested Outcome 与权限容易混淆时，才在 Context 中简短重述相关内容。

Agent 推断不能写成 Human intent 或授权。无法确认的事实前提使用 `[Assumption]`；只分析“如果成立”使用 `[Conditional]`。

## Shared Semantics

按需使用以下共享标签：

| Label | Meaning |
| --- | --- |
| `[Fact]` | 当前 evidence 支持的事实 |
| `[Evidence]` | 可观察依据 |
| `[Decision]` | Human 明确决定 |
| `[Constraint]` | 必须遵守的边界 |
| `[Assumption]` | 暂时采用但尚未证实的前提 |
| `[Conditional]` | 不接受前提，只表达其可能后果 |
| `[Open]` | 未解决的问题或选择 |
| `[Risk]` | 会影响 Human 判断或后续行动的风险 |

标签不是必填字段，也不创建独立 section。未确认状态不能仅因进入 projection 就晋升为 Fact、Decision、Constraint 或授权。Task-specific 标签由对应 template 定义。

## Material Reconciliation

只有 working basis 的协调实质改变当前结果，或不披露会使 Human 误解结论来源时，才使用：

```markdown
### Reconciliation

- Conflict: <什么内容不一致>
- Resolution: resolved | provisional | preserved | unresolved
- Working Basis: <本轮如何继续>
- Basis: <关键可观察依据>
- Effect: <如何影响当前结果>
- Residual: <仍未解决的部分>
```

空字段省略。Routine conflict、工具尝试、调查时间线和隐藏推理不进入 Reconciliation。Reconciliation 不替代 Conditional、Candidate、Risk、Human Attention 或 task-specific deviation，也不机械决定 Status。

## Single-home Rule

每项 material information 只有一个主要归属。可以简短引用，但不能在 Outcome、Context、Reconciliation、Risk 和 Human Attention 中重复展开。

- Outcome：直接答案或最终状态；
- Context：支持答案的 task-specific 内容；
- Reconciliation：working basis 怎样因冲突改变；
- Risk：evidence 尚未关闭的后果；
- Human Attention：只有 Human 能关闭的决定、权限或风险接受。

## Projection Rules

- 结果优先，使用稳定 Markdown headings、English field keys 和用户语言内容；
- 使用 progressive disclosure，只展开会改变 Human 判断的内容；
- 普通 `complete` 保持最短，异常按需展开；
- 当前 conversation 已有的稳定 context 不重复投影；
- 空 section 和空列表直接省略，不生成 `none` 占位；`Human Attention` 是唯一保留 `none` 的公共字段；
- 不输出工具流水账、隐藏 chain-of-thought、完整 handoff packet、评分、dashboard 或自动 next-task；
- 不把 Agent 建议、推断、Plan、Memory、Lens 文件或 template 内容升级为现实授权；effect authorization 只来自 Human 对带 `effects` Lens 的显式选择；
- Task Result 不保存 session、memory、artifact ID 或 source-of-truth，也不自动持久化；显式 Lens 的 declared sidecar 只通过上述 receipt 披露。
