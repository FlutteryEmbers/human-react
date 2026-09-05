# Build Result Projection

Build 返回 Reality Checkpoint 与 Loop Closure Observation。使用 [`common.md`](common.md)；Outcome 直接说明 Requested Outcome 在当前 evidence 下是否以及怎样成立，不以“执行完成”或修改数量代替结果。

## Status

- `complete`：Requested Outcome 已在 Current Reality 中成立，有相称 evidence，所有业务现实干预位于 Authorized Change，且显式 Lens 的 declared effect 已按需完成；
- `partial`：已有有用 Actual Change 或可靠局部结论，但目标、验证或 declared effect 尚未完整关闭；
- `blocked`：无法安全建立必要授权或形成任何有用现实结果与可靠结论。

Status 按 Build 下解释后的现实结果请求判断，实质改写遵循公共 Request Interpretation。验收评价限定于请求与 evidence 覆盖，不扩大为整个产品正确的保证。Actual Change 不证明 Outcome achieved；没有 Actual Change 也不表示失败。

现实修改必须逐项位于 Authorized Change。授权只来自 Human 当前 prompt 明确要求的动作，或该 prompt 无歧义引用且仍有效的既有委托；Build 名称、Task-scoped Request、Plan 和公共模板不能增加现实操作。

## Default Projection

发生现实修改的普通 Build 使用：

```markdown
## Task Result

- Task: build
- Status: complete | partial | blocked
- Outcome: <Requested Outcome 在当前 evidence 下的状态>
- Human Attention: none

### Context

#### Actual Changes

- <stable target>: <实际变化及可观察效果>

#### Verification

- <actual check>: passed | failed | not run | blocked
  - Evidence: <支持 Outcome 的依据>
```

没有现实修改但已验证 Outcome 成立时，省略 Actual Changes。Verification 对 `complete` 必需；`partial / blocked` 中只要它影响判断也必须出现。

理解、诊断、局部设计与规划按需要在同一 Context 中说明结论和依据，不叠加其他 Task Result。只要求检查且不修改时，交付实际验证结果；如果请求仍要求实现尚未成立的目标而修改被禁止，不取消限制或伪称目标成立，披露解释后请求的实际未完成部分。

`[Change]` 表达实际状态变化，`[Verification]` 表达实际执行或要求的检查；它们不是 Planned Change 或未来建议。

## Verification Boundary

- Verification 记录实际方法、验收判断及其支持的 claim 与覆盖边界，不只记录命令退出状态；
- Plan Checks 默认是推荐方法；coverage 等价的环境适配可以替换，Human 指定的方法属于 acceptance 时不得静默替换；
- Diagnostic artifact 必须证明可运行、目标 observation 出现，并排除 syntax、fixture、setup、import 或 environment failure；
- Failing test 只支持给定 instrument、input、environment 和 dependency state 下的 observation，没有 baseline 时不自动证明 defect、root cause 或 regression；
- `not run / blocked` 说明缺少什么以及 completion claim 的边界；较弱 fallback 不得被描述成完整行为验证；
- Verification blocked 不机械决定整个 Status：已有有用现实结果时通常为 `partial`，充分替代 evidence 可以支持 `complete`，没有有用结果且关键验证无法进行时才为 `blocked`；
- Delayed effect 属于 acceptance 时需要直接 observation 或有效 proxy，否则不能 `complete`。

## Exception Projection

内部 Observable Boundary Gate、Investigate 过程、patch 编号和 coupling trace 不输出。异常严格遵循 single-home：

| Information | Primary Home |
| --- | --- |
| 当前 Authorized Change 未完成或发生 material deviation | `Incomplete / Deviated` |
| Verification 后仍存在的不确定性、coverage gap 或未清理 effect | `Remaining Risk` |
| 冲突导致 working basis 或五项可观察边界改变 | `Reconciliation` |
| 需要 Human decision、扩权或风险接受 | `Human Attention` |
| scope 外、可能成为新 loop 输入的独立问题 | `Loop Closure / Remaining Gap` |
| 依据所选 task 对动作诉求的实质改写 | `Request Interpretation` |

同一事实不能在这些位置重复展开。改写说明不是前置审批；被转为其他交付内容的原文动作不自动进入 Remaining Gap 或 Incomplete / Deviated。Routine means-level adaptation、省略的 stale Planned Change 和稳定边界内的 companion edit 不投影。

Reconciliation 使用公共结构，并将多个相关 coupling signal 聚合为一次对 Human 有用的说明。Material deviation 的现实结果进入 Incomplete / Deviated；需要 Human 处理的 residual 只在 Human Attention 简短引用。

## Loop Closure

简单 direct Build 已由 Outcome、Actual Changes（如有）和 Verification 闭合时，省略独立 Loop Closure。只有当前 loop 存在 material Review、Shape、Plan 或 Human Decision 演化时才按需说明：

- `Started From`：最初要关闭的 gap；
- `Material Shift`：真正改变执行的 Decision、Constraint、system evidence 或已披露 Reconciliation；
- `Remaining Gap`：scope 外、可能成为新 loop 输入的开放状态。

Loop Closure 不评价前序 task，不重写 Starting Gap，不自动发起下一 loop，也不宣称未来状态永久成立。

## Reusable Insight

成功解决的 operational friction 同时具备 material、可能复发、成功 evidence 和明确适用边界时，使用：

- `[Lesson]`：已有成功 working resolution；
- `[Heuristic]`：只有有限案例支持的候选经验；
- `[Invariant]`：由独立项目规范、Human-confirmed Constraint 或修改前已充分建立的 evidence 支持。

内容压缩为 Trigger、Resolution、Evidence 和 Boundary，不复制 Verification 或试错历史。失败路径只有在能避免未来重复昂贵、越权或无信息增益的尝试时保留。本轮新代码或测试不能单独证明 Invariant；只有 Human 显式选择 `memory-capture` 时，符合其 Capture Gate 的内容才写入本轮 declared sidecar。

## Human Attention

只放置需要 Human 决定、授权或接受的事项，例如新 scope、external contract、Compatibility Boundary、不可逆操作、重要风险或新的稳定责任边界。普通实现选择和 scope 外 Remaining Gap 不自动成为 blocker。

Build 不输出完整 execution trace、环境报告、self-audit、自动 routing 或 persistence 建议。
