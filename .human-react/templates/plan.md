# Plan Result Projection

Plan 返回 Execution Checkpoint。使用 [`common.md`](common.md)；Outcome 说明 Target Outcome、当前选择和是否存在 Required Delta。Plan Result 是 Planned Change，不构成 Build authorization。

## Status

- `complete`：Required Delta 已可靠判断；需要修改时，Execution Model 足以执行和验证；
- `partial`：已有可用的局部 delta 或方案，但 Human-owned decision 或 material evidence gap 仍限制执行；
- `blocked`：无法识别目标、委托或形成任何有用模型。

`complete` 可以表示当前目标已经满足、无需现实修改，也不表示 Human 接受全部 planning reasoning。

## Default Projection

存在 Required Delta 的普通 Plan 使用：

```markdown
## Task Result

- Task: plan
- Status: complete | partial | blocked
- Outcome: <目标、选择路径和预期效果>
- Human Attention: none

### Context

#### Change Surface

- `<stable target>`: <intended change> — <当前 gap 与修改必要性>

#### Verification

- Success Criteria: <应成立的可观察结果>
- Checks: <推荐的可执行验证方法>
```

多修改面可以将 Change Surface 改为 `Target | Intended Change | Reason` 表格。每个 target 必须对应 observable unmet condition；companion target 的 Reason 还要说明它为何是关闭同一 Required Delta 不可缺少的修改。受影响但无需改变的对象不进入 Change Surface。

没有 Required Delta 时，Outcome 直接说明目标已满足，Context 只保留支持该判断的 Verification；不生成空 Change Surface、Chosen Approach 或 no-op Execution Model。

## Optional Context

- `Planning Basis`：只保留会改变实施的 Target Outcome、Constraint、Human Decision、Assumption 或 Compatibility Boundary。
- `Chosen Approach`：存在重要路径选择时说明整体 Approach；Agent 在 delegation 内关闭的选择使用 `Resolved Choice + Basis`，不使用 `[Decision]`。
- `Execution Model`：只有多个 work package、必要依赖或顺序时出现；按结果组织，不展开文件级微步骤和工具流水账。
- `Scope`：只有重要 Allowed Changes、Do Not Touch 或 Out of Scope 时出现。Scope 是权限边界，不代替 Change Surface。
- `Risks / Stop Conditions`：只保留会改变 Build 行为、授权或失败处理的 material coupling signal；Stop Condition 将其表达为 Build 可观察的停止边界。
- `Reconciliation`：requested target、delegation、Candidate、Boundary、strategy、dependency 或 verification 冲突并实质改变计划时使用公共结构。

Success Criteria 定义 Verification Obligation；Checks 是推荐方法，不是固定命令，除非 Human 明确把平台、runner、command 或 environment 纳入 acceptance。Fallback 或 Residual Risk 只在主要方法不可用或 coverage 不充分时出现。

Compatibility 不使用专用 section。已建立 Boundary 放入 Planning Basis，Mechanism 作为 Resolved Choice，相关行为由 Verification 覆盖。已知 external contract 将被改变但 Boundary 未建立时返回 `partial` 并进入 Human Attention。

## Multi-round Projection

局部修订只输出变化内容；一旦 Change Surface 改变，必须返回完整当前 Change Surface，并同步受影响的 Execution Model、Scope 和 Verification。

## Human Attention

只放置必须由 Human 确定的 Target Outcome、价值、产品或领域语义、scope、external contract、Compatibility Boundary、权限或重要风险接受。可由 Agent 在既有边界内关闭的技术选择不成为 blocker。

Plan 不输出 Confirmed、routing、persistence、external handoff 或默认兼容政策。
