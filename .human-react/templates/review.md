# Review Result Projection

Review 返回面向 Human 的 Evidence Checkpoint。使用 [`common.md`](common.md)；Outcome 默认表达 evidence-backed answer，只有 review question 本身是条件问题时才使用显式条件结论。

## Status

- `complete`：问题已回答或不可判定的证据边界已可靠建立，且显式 Lens 的 declared effect 已按需完成；
- `partial`：已有有用 finding，但核心问题或 declared effect 仍未关闭；
- `blocked`：无法识别或访问 target，或缺少 Human 必须提供的 baseline、decision 或权限，且不能形成任何有用判断。

Status 评价 Review 本身，不评价 target。完整证明 target 不合格或存在 `[Blocking]` finding 时仍可为 `complete`。

## Default Projection

普通 Review 使用一个或多个“判断与 evidence 紧邻”的 finding：

```markdown
## Task Result

- Task: review
- Status: complete | partial | blocked
- Outcome: <对 review question 的直接回答>
- Human Attention: none

### Context

- [<classification when useful>] <finding>
  - [Evidence] <可观察依据>
  - [Gap] <expected 与 actual 的差异；适用时>
  - [Diagnosis] <有因果证据时>
  - Repair Direction: <已有 gap 且确有帮助时>
```

简单事实或单一 diagnosis 不要求 classification。Gap、Diagnosis 和 Repair Direction 默认放在对应 finding 下；只有它们跨多个 finding 才单独组织，不能复制原 finding。

## Finding Semantics

- `[Blocking]`：有 evidence 证明 finding 阻止明确 intended use，并紧邻说明 `Blocks`；
- `[Material]`：当前用途仍可继续，但会造成实质风险、歧义、漂移或返工；
- `[Minor]`：影响表达、清晰度或便利性；
- `[Validated]`：已检查的重要方面没有实质 gap；
- `[Diagnosis]`：有 evidence 支持的原因判断。

Classification 只用于 evidence-backed finding，表达 disposition，不表达确定度，也不替代 Gap、Diagnosis 或 `[Risk]`。空等级、完整 pass checklist 和独立 Blocking/Non-blocking dashboard 都省略。

## Optional Context

- `Use Verdict`：只在问题询问 intended-use fitness 时放在 Context 开头：存在 Blocking 为 `blocked`；只有 Material 为 `usable-with-caveats`；只有 Minor/Validated 或无实质问题为 `usable`；证据不足为 `undetermined`。Outcome 说明实质原因，不重复列表。
- `Target / Question`：target 复杂、多问题、歧义或 `partial / blocked` 时使用。
- `Reconciliation`：evidence 冲突实质改变 finding、Diagnosis、Use Verdict 或 evidence boundary 时使用公共结构。
- `Conditional Analysis`：仅在方向性 premise 有信息价值时列出 `Premise / If accepted / Not established`；它不成为 finding 或 Diagnosis。
- `Uncertainty`：只保留会改变结论、范围或下一轮选择的未检查范围、替代解释和 evidence 上限。
- `Follow-up Options`：只有后续方向能显著降低关键不确定性时使用，通常不超过 2 项；`Possible task` 仅在映射明确时出现。

Repair Direction 只对应 evidence-backed gap，并保持为最小方向。目标属性已经成立时使用 `[Validated]` 或 Outcome，不把 preserve requirement 包装成修复。

## Human Attention

只放置需要 Human 决定、授权或接受风险的事项。普通未知进入 Uncertainty；`[Blocking]` finding 也不会仅因分类自动成为 Human blocker。

Review 不输出完整替代设计、实施计划、工具流水账或自动后续行动。
