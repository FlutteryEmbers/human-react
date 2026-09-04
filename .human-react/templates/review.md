# Review Result Projection

Review 结果是面向 Human 和当前 conversation 的 Evidence Checkpoint。它应让 Human 一眼看到当前结论、证据边界和必要的后续选项，不需要脱离对话后仍然自包含。

使用 [`common.md`](common.md) 定义的公共 Task Result。`Outcome` 默认直接表达 evidence-backed answer；只有 review question 本身明确是条件问题时，才可以输出显式标注的条件结论。不要用“已完成 review”之类过程描述代替结论。

## Status

- `complete`：问题已得到回答，或证据不足的边界已被可靠建立；
- `partial`：已有有用 finding，但核心问题仍未解决；
- `blocked`：无法访问或识别 target，或缺少 Human 必须提供的 baseline、decision 或权限，且当前无法形成有用判断。

`Status` 评价 Review task 的完成度；`Outcome` 才表达 target 的实际状态。`Status: complete` 可以与“target 不合格”或“根因仍无法确认”同时成立。

完整证明一个 `[Blocking]` finding 时，Review 仍应为 `Status: complete`。`Status: blocked` 只表示 Review 本身因 target、baseline、decision 或权限问题无法形成有用判断。

## Context Projection

`Context` 只投影当前 review 新产生且对 Human 判断有价值的内容。根据问题按需组织，不强制填满 section。

### Use Verdict

只在 review question 明确要求判断 target 是否适合某个 intended use 时投影，并优先放在 `Context` 开头：

```text
Use Verdict: usable | usable-with-caveats | blocked | undetermined
Intended Use: <被判断的具体用途>
```

推导规则：

- 存在至少一个 `[Blocking]` finding：`blocked`；
- 没有 Blocking，但存在 `[Material]`：`usable-with-caveats`；
- 只有 `[Minor]` / `[Validated]`，或没有实质问题：`usable`；
- baseline、evidence 或 intended use 不足以形成判断：`undetermined`。

`Outcome` 仍直接回答 review question，并说明支持 verdict 的实质原因。Use Verdict 只提供快速扫描，不替代 finding 和 evidence。不存在 intended-use fitness 问题时省略整个 section，不输出 `not-applicable`。

### Target / Question

只在 target 复杂、范围易混淆、请求包含多个问题，或结果为 `partial` / `blocked` 时显式投影。短且明确的请求不重复复述。

### Findings and Evidence

这是 Review Context 的主体。在多 finding 审计、gap analysis 或 intended-use fitness review 中，优先用 disposition + finding + evidence 紧邻结构：

```markdown
- [Blocking] <有证据支持、会阻止明确 intended use 的 finding>
  - Blocks: <被阻止的具体 intended use>
  - [Evidence] <可观察依据、位置或验证结果>

- [Material] <不阻止当前用途，但会导致实质歧义、返工、漂移、风险或维护成本的 finding>
  - [Evidence] <支持证据>

- [Minor] <清晰度、表达、便利性或非必要完整性问题>
  - [Evidence] <支持证据>

- [Validated] <已检查且没有发现实质 gap 的重要方面>
  - [Evidence] <验证依据>
```

不要求每份 Review 使用全部类型，也不为空类型生成 section 或 `none`。简单事实结果、单一 finding 或单一 diagnosis 可以直接使用短段落或列表，不强制 classification。

`[Blocking]` 必须说明 `Blocks`；没有明确 intended use 时不使用。`[Validated]` 只投影与 review question 直接相关的重要已检查内容，不生成 pass checklist。多个证据共同支持一个 finding 时，按 finding 聚合，不按工具调用顺序编排。

Classification 只能用于 evidence-backed finding，表达它对当前用途的处置意义；它不表达证据确定度，也不替代 gap、diagnosis、impact 或 `[Risk]`。Assumption、hypothesis 和 Conditional Analysis 不得直接使用 `[Blocking]` 或 `[Material]`。

不再将已分类 findings 复制到独立的 Blocking Gaps、Non-blocking Gaps 或其他 dashboard。

### Reconciliation

当文档、代码、测试、运行产物、Human claim 或其他 evidence 之间的冲突实质影响 finding、diagnosis、Use Verdict 或 evidence boundary 时，按公共 [material reconciliation disclosure](common.md#material-reconciliation-disclosure) 投影 Evidence Reconciliation。

- `Working Basis` 说明当前 Review 如何使用这些 evidence，不宣称已建立新的 source-of-truth policy；
- `Basis` 只投影对 Human 判断有价值的 role、scope、version、directness 或 reproducibility 依据，不输出完整 evidence ledger；
- normative 与 observed evidence 的差异应在 `Effect` 中说明它是 expected/actual gap，不得伪装成某份 evidence 已被自动废弃；
- unresolved normative choice 需要 Human 时，同时进入 `Human Attention`；只有事实不确定且不需要 Human decision 时，留在 `Residual` 或 `Uncertainty`。

### Gap

需要比较时，简明表达：

```text
Expected → Actual → First material divergence / Impact
```

不需要 gap analysis 的 Review 省略此内容。

### Diagnosis

已有足够因果证据时，说明直接原因、更深层机制和证据支持程度。只有候选解释时应写为 hypothesis 或 alternative explanation，不得投影为已确认 diagnosis。

### Conditional Analysis

只在 Human 给出了方向性 premise，或条件推演能帮助理解关键分支时投影：

```markdown
### Conditional Analysis

- Premise: <本段推演暂时接受什么>
- If accepted: <从该前提可以推出的逻辑链>
- Not established: <本次推演没有证明什么>
```

它不是 finding、diagnosis 或已确认事实。如果 premise 与 evidence 冲突，在本 section 中明确说明；不得为了完成条件链而隐去反证。没有方向性 premise，或推演不会改善 Human 判断时，省略整个 section。

### Uncertainty

仅投影会改变结论、范围或 Human 下一轮选择的未知，包括：

- 未检查的重要范围；
- 尚不能排除的替代解释；
- 证据能支持的结论上限；
- 可能影响判断的取证副作用或环境限制。

### Repair Direction

只在一个最小修复方向能帮助 Human 理解 finding 或判断后续时投影。它可以紧邻对应 finding，但不展开实施步骤、文件级计划或完整备选设计。

### Follow-up Options

只在后续探索或决策方向有实质价值时投影，通常不超过 3 项。每项应说明要做什么，以及它将回答什么问题或降低什么不确定性：

```markdown
### Follow-up Options

- <可能行动>，用于 <将被解答的问题>。
  - Possible task: orient | review | shape | plan | build
```

`Possible task` 只在映射明确且确实帮助 Human 选择时使用，可以省略。Follow-up Options 不是命令、默认路径或授权；输出它们后立即 Handback，不自动继续。

## Human Attention

只放置必须由 Human 决定、授权或承担风险的事项。可由 Agent 在当前 review scope 内继续检查的普通未知不应伪装成 Human blocker；它们应进入 `Uncertainty` 或 `Follow-up Options`。`[Blocking]` finding 也不会自动进入 `Human Attention`；只有它需要 Human 作出决定、提供授权或接受风险时才投影到该字段。

## Compact Shape

一份典型结果可以是：

```markdown
## Task Result

- Task: review
- Status: complete | partial | blocked
- Outcome: <对 review question 的直接回答>
- Human Attention: <必要的 Human decision/authorization；没有则为 none>

### Context

Use Verdict: <只在 intended-use fitness review 中使用>
Intended Use: <适用时>

- [Blocking] <阻止明确 intended use 的 finding>
  - Blocks: <intended use>
  - [Evidence] <支持证据>
- [Material] <不阻止但具有实质影响的 finding>
  - [Evidence] <支持证据>
- [Minor] <轻微问题>
  - [Evidence] <支持证据>
- [Validated] <已验证的重要方面>
  - [Evidence] <支持证据>
- [Gap] <适用时>
- [Diagnosis] <证据足够时>
- [Uncertainty] <会影响后续判断时>

### Conditional Analysis

- Premise: <适用时>
- If accepted: <条件逻辑链>
- Not established: <未被证明的部分>

### Follow-up Options

- <有信息价值的可能行动；没有则省略整个 section>
```

这是可裁剪的 projection shape，不是必填表单。除公共头部外，无内容的 section 直接省略。
