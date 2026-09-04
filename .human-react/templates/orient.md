# Orient Result Projection

Orient 结果是面向 Human understanding 的 Scoped Explanatory Model。它帮助 Human 看懂当前 Subject，不形成 Review verdict、Shape Decision Space、Plan Execution Model 或 Build authorization。

使用 [`README.md`](README.md) 定义的公共 Task Result 头部。`Outcome` 直接回答 Learning Question，不使用“已完成分析”“提供如下背景”等过程描述代替解释结果。

## Status

- `complete`：当前 Learning Question 已获得最小充分、有边界的 explanatory model；
- `partial`：已经形成有用解释，但 material target context、evidence 或 Human meaning 仍不完整；
- `blocked`：无法识别 Subject / Learning Question，或缺少关键条件而不能形成任何有用解释。

`complete` 不表示 Subject 已被穷尽、解释不可修正，或已经完成评价、决策和行动。

## Context Projection

### Learning Focus

仅在 Subject、Learning Question 或 Intended Understanding 复杂、容易漂移或存在歧义时投影：

```markdown
### Learning Focus

- Subject: <本轮被解释的对象>
- Question: <Human 想形成的理解>
```

简单请求由 Outcome 直接保持 focus，不重复这一 section。

### Understanding Model

Orient 的主要结果。除 `blocked` 且无法形成任何解释外，使用简洁 prose、列表、关系式或必要的小型图示说明关键术语、组成部分、机制、过程和关系：

```markdown
### Understanding Model

<对当前 Learning Question 的结构化解释>
```

- 先解释最影响理解的结构，不按检索顺序组织内容；
- 使用与 Human 当前知识水平相称的粒度；
- 不把详细程度等同于穷尽程度；
- 简单问题可以只使用 Outcome 加一段 Understanding Model。

### Relevant Background

只有背景知识直接改善当前解释时使用：

```markdown
### Relevant Background

- <背景知识> — Relevance: <它为什么影响当前理解>
```

不生成百科式背景、通用阅读清单或与 Learning Question 没有明确关系的信息。

### Alternative Perspectives

只有 materially different perspective 能改变 Human 看见的结构、边界或问题时使用，通常不超过 3 项：

```markdown
### Alternative Perspectives

- <perspective>: <它使什么内容变得可见>
  - Boundary: <不能据此建立什么>
```

Perspective 不形成 Candidate、recommendation、Decision Criteria 或 Resolved Choice。措辞差异和没有新解释价值的角度直接省略。

### Understanding Boundary

当不同认识状态可能被误读为同一种事实时使用：

```markdown
### Understanding Boundary

- Target-specific: <有当前 target evidence 支持的内容>
- General model: <尚未证明适用于当前 target 的通用知识>
- Interpretation: <Agent 对已有内容的解释性综合>
- Unknown: <当前没有建立的内容>
```

空项省略。该 section 不建立新的 source-of-truth policy，也不复制完整 evidence ledger。普通 `[Fact]`、`[Evidence]`、`[Assumption]` 和 `[Conditional]` 仍按公共语义使用。

### Reconciliation

General model、Human assertion、documentation 与 target evidence 的冲突实质改变 Understanding Model 时，按公共 [material reconciliation disclosure](README.md#material-reconciliation-disclosure) 投影：

- working basis 只服务当前 explanation；
- target-specific conclusion 不被通用模型静默覆盖；
- `provisional` 或 `unresolved` 的边界按需进入 Understanding Boundary；
- 需要 Human 明确 meaning 或 intended use 时同时进入 Human Attention。

Routine terminology normalization 和不影响结果的局部差异不生成 Reconciliation。

## Multi-round Projection

同一 conversation 中的 follow-up Orient 默认只补充或修正被追问的 Understanding Model、Relevant Background、Perspective 或 Boundary。只有 Human 要求总结、旧解释发生 material conflict，或局部回复将造成整体误解时才重建 consolidated model。

## Human Attention

只放置必须由 Human 明确的 Subject、meaning、intended use 或 materially different learning direction。可以通过当前 context 或只读检查消除的普通未知不应成为 Human blocker。

## Compact Orient

```markdown
## Task Result

- Task: orient
- Status: complete | partial | blocked
- Outcome: <对 Learning Question 的直接回答>
- Human Attention: <需要 Human 明确的含义；没有则为 none>

### Context

### Understanding Model

<结构化解释>

### Relevant Background

- <只在直接改善理解时>

### Alternative Perspectives

- <只在 materially different 时>

### Understanding Boundary

- <只在认识状态可能混淆时>

### Reconciliation

<只在 material conflict 时>
```

这是可裁剪的 projection shape，不是必填表单。普通 Orient 通常只需要公共头部和 Understanding Model；不生成 verdict dashboard、Decision Space、实施建议、reading list、检索流水账、持久化建议或自动 next-task。
