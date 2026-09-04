# Build Result Projection

Build 结果是当前 delivery loop 的 Reality Checkpoint 与 Loop Closure Observation。它首先让 Human 看见现实中发生了什么、证据支持什么和哪些边界仍然存在；只有 material multi-task 演化需要压缩时，才增加独立 Loop Closure。

使用 [`common.md`](common.md) 定义的公共 Task Result。`Outcome` 直接表达 Requested Outcome 在当前 evidence 下是否以及如何实现，不用“已执行 Build”、Actual Change 存在或修改数量代替结果。

## Status

- `complete`：Requested Outcome 已通过 Authorized Change 实现，并有与风险和 claim 相称的 evidence 支持；
- `partial`：已经产生有用 Actual Change，但目标、验证或部分授权工作未完整关闭；
- `blocked`：无法安全建立 Authorized Change，或在产生有用现实结果前遇到必须由 Human 处理的边界。

`Status` 只评价当前 Requested Outcome，不评价 target 整体是否正确或适用，也不评价整个项目是否完成。Actual Change 已发生不等于 Requested Outcome 已实现；明确位于当前 acceptance 或 scope 外的 Remaining Gap 可以与 `complete` 同时存在。

## Context Projection

`Context` 只投影现实状态、验证边界和当前 loop 的 material delta。Requested Outcome、Authorized Change 或 current scope 容易混淆、结果为 `partial / blocked`，或实际执行范围与 Plan 不同时，先用简短自然语言明确当前 execution basis；不增加固定授权表单。

### Actual Changes

发生现实修改时必须投影：

```markdown
### Actual Changes

- <stable target>: <实际变化及可观察效果>
```

- 按行为、接口、模块或 Human 能判断影响的稳定 target 描述，不默认复制完整文件清单；
- 预期且持久的现实效果写在这里；只写已经发生的变化，不把 Planned Change、已经完全撤销且无残留的试验或未来建议混入；
- 撤销 repo diff 后仍存在的 generated artifact、cache、watcher、hook、external state 或 temporary resource 必须按真实影响进入 Actual Changes、Incomplete / Deviated 或 Remaining Risk，不能据此报告 no change；
- `blocked` 且没有现实变化时省略整个 section，由 Outcome 直接说明 no change。

### Verification

`complete` Build 必须包含 Verification；`partial` 或 `blocked` 中，只要验证状态影响 Human 判断也必须投影：

```markdown
### Verification

- <check>: passed | failed | not run | blocked
  - Evidence: <支持结果判断的可观察依据>
```

- Check 可以是 targeted test、static check、build、smoke check 或必要 manual observation；
- Plan 的 Success Criteria 表达 Verification Obligation，Checks 默认只是推荐的 Verification Method。Build 可以根据 Actual Environment 替换 shell syntax、path、runner 或 evidence coverage 等价的方法；Human 明确将特定 platform、runner、command 或 environment 纳入 acceptance / Constraint 时不得静默替换；
- Verification 记录实际采用的方法和它支持的 claim。OS、shell、cwd、runner、version、configuration、command source 和替换依据只在异常、歧义、coverage 或复现价值实质影响判断时展开；
- `passed` 必须支持 Requested Outcome，而不只是命令正常退出；
- persistent test、fixture、instrumentation、diagnostic command、reproduction harness 或复现配置作为 Build outcome 时，必须证明 artifact 可运行、目标 observation 出现，且结果来自预期 discriminating condition，而不是 syntax、fixture、setup、import 或 environment failure；
- failing test 只证明相应 instrument、input、environment 和 dependency state 下的 observation。没有 established expected behavior 或 baseline 时，不能据此声称 product defect、root cause 或 regression；
- delayed effect 属于 acceptance 时，`passed` 需要直接 observation 或足以支持该 claim 的有效 proxy；证据不足时不得返回 `complete`；
- `not run` 与 `blocked` 必须说明缺少什么，以及它如何限制 completion claim；
- 原方法不可用但替代 evidence coverage 等价时可以继续，只有适配会影响 Human 判断时才展开说明；
- fallback 较弱但仍足以支持 Requested Outcome 时，可以 `complete` 并披露 Remaining Risk；不足以支持时返回 `partial`，不能仅通过降级 verification 隐藏失败；
- `Verification: blocked` 不机械决定 task Status：已有有用 Actual Change 时通常为 `partial`，存在充分替代 evidence 时仍可 `complete`，没有有用现实结果且关键验证无法进行时才为 `blocked`；
- capability problem 应先寻找当前权限内的非特权替代。管理员权限、凭据、新网络访问、机器级配置、安全策略变化或不同 external-effect profile 需要 Human authorization。

### Incomplete / Deviated

只在 Authorized Change 未完整关闭或发生 material implementation deviation 时投影：

```markdown
### Incomplete / Deviated

- <未完成、跳过或偏离的内容>: <原因及对结果的影响>
```

Routine means-level adaptation 不进入这里。环境或权限障碍导致当前 Authorized Change 未完成时进入这里；已经发生的 material unintended effect 也必须进入这里，即使相关代码修改随后被撤销。该 section 只核对当前 Authorized Change 及其实际偏差，不收集 scope 外未来工作。

### Remaining Risk

Verification 后仍有会影响 Human 判断的风险，或现实 effect 无法完全观察、验证或清理时投影：

```markdown
### Remaining Risk

- [Risk] <仍未被 evidence 关闭的风险及适用范围>
```

已在 Incomplete / Deviated 或 Reconciliation 中充分表达的内容不重复。无法验证的范围、环境限制与较弱 fallback 的 coverage gap 按需进入这里。Delayed effect 位于当前 acceptance 外但仍是可能后果时，说明 observation window 与当前 evidence 能支持到哪里；它可以与 `complete` 并存。

### Loop Closure

每次 Build 在语义上都产生 Loop Closure Observation。简单 direct Build 已由 Outcome、Actual Changes 和 Verification 清楚表达开始与结束状态时，省略独立 section。当前 delivery loop 包含 material Review、Shape、Plan 或 Human decision 演化时，使用：

```markdown
### Loop Closure

- Started From: <本轮最初要关闭的 gap>
- Material Shift: <实际改变执行方向的理解、Constraint 或 Human Decision>
- Remaining Gap: <现实变化后仍存在、可能成为新 loop 输入的差异>
```

- 无变化的字段省略，不输出 `none`；
- Outcome 与 Actual Changes 已表达最终现实状态，因此不增加重复的 End State；
- `Material Shift` 只保留与 Actual Change 有直接因果关系、且能对应到可观察 Human Decision、Constraint、system evidence 或已披露 Reconciliation 的变化，不评价 Review、Shape 或 Plan 是否正确；
- 实质重新解释 Starting Gap、Human Decision 或原有 Boundary 时必须使用 Reconciliation，不能通过事后总结静默重写前序 context；
- `Remaining Gap` 描述当前 loop 结束后的开放状态，不等于当前 Authorized Change 未完成，也不会机械降低 Status；
- 多个并行目标只总结当前 Authorized Change 对应的链条，不合并完整 conversation，不生成时间线；
- Loop Closure 只表示当前 evidence 下的 provisional closure，不宣称未来状态永久成立；它返回 Human，不包含固定 task recommendation、自动 continuation 或新授权。

### Reconciliation

plan、code、test、tooling、runtime reality 或 dirty worktree 之间的冲突实质改变 Actual Changes、Verification、Incomplete / Deviated、Remaining Risk 或 Loop Closure 时，按公共 [material reconciliation disclosure](common.md#material-reconciliation-disclosure) 投影。Plan check 与 Actual Environment 的差异只有在改变 evidence strength、assumption、Execution Model 或 Human 判断时才需要 Reconciliation；普通命令改写不生成该 section。

- Routine means-level adaptation 不输出 Reconciliation；
- working basis 不能创建新目标、scope、权限、Compatibility Boundary 或风险承诺；
- material deviation 同时在 Incomplete / Deviated 中说明实际结果；
- 需要 Human 决定或授权的 residual 同时进入 Human Attention。

### Reusable Insight

本轮产生了有 evidence 支持、适用范围明确且会改变未来行动的认识时投影。成功解决的 operational friction 同时满足 material、可能复发、已有成功 evidence、适用边界可说明时必须投影；routine path correction、typo 和无复用价值的工具尝试继续省略：

```markdown
### Reusable Insight

- [Heuristic] When <trigger>, try <candidate resolution>; Evidence: <有限观察>. Boundary: <尚未验证的范围>.
- [Lesson] When <trigger>, use <working resolution>; verified by <evidence>. Boundary: <applicable conditions>.
- [Invariant] <由独立规范、Human-confirmed constraint 或修改前已充分建立的 evidence 支持的稳定约束>
```

只选择与证据强度匹配的标签，不要求三类同时出现。Reusable resolution 必须包含 Trigger、Working Resolution、Evidence 和 Applicability Boundary，但可以压缩为一行并引用 Verification 的结果，不复制完整验证记录。失败路径只有在能避免未来重复昂贵、越权或无信息增益的尝试时保留，不形成执行时间线。本轮新发现默认使用 `[Heuristic]` 或 `[Lesson]`；本轮新写入的代码或测试不能成为新 `[Invariant]` 的唯一证明。通用套话、工具流水账和对 Outcome 的复述不算 insight；内容不会自动写入 Memory、Lens 或项目文档。

## Human Attention

只放置需要 Human 决定、授权或接受的事项，例如新 scope、未授权 external contract、Compatibility Boundary、不可逆操作、重要风险接受，或无法在当前权限内关闭的 dirty-worktree 冲突。普通实现选择、已自主解决的局部偏差和 scope 外 Remaining Gap 不自动成为 Human blocker。

## Compact Build

```markdown
## Task Result

- Task: build
- Status: complete | partial | blocked
- Outcome: <Requested Outcome 在当前 evidence 下是否以及如何实现>
- Human Attention: <必要的 Human decision/authorization；没有则为 none>

### Context

### Actual Changes

- <target>: <实际变化及可观察效果>

### Verification

- <check>: passed | failed | not run | blocked
  - Evidence: <必要依据>

### Incomplete / Deviated

- <只在相关时>

### Remaining Risk

- [Risk] <只在相关时>

### Loop Closure

- Started From: <material multi-task loop 时>
- Material Shift: <实际影响执行的变化>
- Remaining Gap: <loop 结束后的开放状态>

### Reconciliation

<只在 material conflict/deviation 时>

### Reusable Insight

- [Heuristic | Lesson] When <trigger>, <resolution>; Evidence: <依据>. Boundary: <适用范围>.
```

这是可裁剪的 projection shape，不是必填表单。普通成功 Build 通常只需要公共头部、Actual Changes 和 Verification；不生成完整 execution log、环境报告、step checklist、summary packet、handoff packet、持久化建议或自动 next-task。
