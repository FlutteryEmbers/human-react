# Plan Result Projection

Plan 返回 Execution Checkpoint。使用 [`common.md`](common.md)；Outcome 说明 Target Outcome、当前选择和是否存在 Required Delta。Plan Result 是 Planned Change，不构成 Build authorization。

## Status

- `complete`：Required Delta 已可靠判断；需要修改时，Execution Model 足以执行和验证；
- `partial`：已有可用的局部 delta 或方案，但 Human-owned decision 或 material evidence gap 仍限制执行；
- `blocked`：无法识别目标、委托或形成任何有用模型。

Status 按 Plan 下解释后的规划请求判断，实质改写遵循公共 Request Interpretation；“修好”等原文动作被转为规划对象而未实施，不单独降低状态。`complete` 可以表示目标已经满足、无需修改，也不表示 Human 接受全部 planning reasoning。

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

没有 Required Delta 时，Outcome 直接说明目标已满足，Context 保留支持该判断的 Verification 及本轮必需的 Request Interpretation；不生成空 Change Surface、Chosen Approach 或 no-op Execution Model。

## Optional Context

- `Planning Basis`：只保留会改变实施的 Target Outcome、Constraint、Human Decision、Assumption、Compatibility Boundary，以及必要诊断或局部模型的结论与依据。影响验收的建议门槛或未决依据在此按需标明，不冒充 Human 要求。会影响实施的暂定前提说明来源及其改变后的影响；Human-owned 选择保持未决，条件方案不表示可直接执行。
- `Chosen Approach`：存在重要路径选择时说明整体 Approach，原机制不成立时说明替代机制及比较依据；重要选择在关闭前检查依据与实际影响是否在 delegation 内；合法关闭的选择使用 `Resolved Choice + Basis`，不使用 `[Decision]`，不叠加 Shape Result。
- `Execution Model`：只有多个 work package、必要依赖或顺序时出现；按结果组织，不展开文件级微步骤和工具流水账。
- `Scope`：只有重要 Allowed Changes、Do Not Touch 或 Out of Scope 时出现。Scope 是权限边界，不代替 Change Surface。
- `Risks / Stop Conditions`：只保留会改变 Build 行为、授权或失败处理的 material coupling signal；Stop Condition 将其表达为 Build 可观察的停止边界。
- `Reconciliation`：requested target、delegation、Candidate、Boundary、strategy、dependency 或 verification 冲突并实质改变计划时使用公共结构。

Success Criteria 呈现有请求、约定或目标与 evidence 推导依据的已建立验收条件；未确定的建议门槛留在 Planning Basis，不直接形成义务或新增修改目标。Success Criteria 定义 Verification Obligation；Checks 是推荐方法，不是固定命令，除非 Human 明确把平台、runner、command 或 environment 纳入 acceptance。Fallback 或 Residual Risk 只在主要方法不可用或 coverage 不充分时出现。

Compatibility 不使用专用 section。已建立 Boundary 放入 Planning Basis，Mechanism 作为 Resolved Choice，相关行为由 Verification 覆盖。已知 external contract 将被改变但 Boundary 未建立时返回 `partial` 并进入 Human Attention。

## Multi-round Projection

局部修订默认输出变化内容，并保留本轮必需的 Request Interpretation；一旦 Change Surface 改变，必须返回完整当前 Change Surface，并同步受影响的 Execution Model、Scope 和 Verification。

## Required Content And Compression

首次、无 Required Delta 与局部续轮均保留公共外壳。存在 Required Delta 的完整结果必须交付 Change Surface 和 Verification；无 delta 也保留支持结论的 Verification。一旦 Change Surface 改变，不能以“只说变化”为由省略完整当前修改面及受影响内容；真正无法形成的内容说明缺口，不生成空字段。所有结果保留公共条件必需披露。

## Human Attention

沿用公共定义，只保留最终未决事项；已解决的决定、仅需知悉的风险与可自主处理的暂定依据归对应 Context。

只放置必须由 Human 确定的 Target Outcome、价值、产品或领域语义、scope、external contract、Compatibility Boundary、权限或重要风险接受。可由 Agent 在既有边界内关闭的技术选择不成为 blocker。

将实施措辞解释为规划请求不产生 Human Attention；必要诊断与边界内机制替换也不单独触发交接或 `partial`。

Plan 不输出 Confirmed、routing、persistence、external handoff 或默认兼容政策。
