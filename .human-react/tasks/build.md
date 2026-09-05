# Task: build

> Requested Outcome + Current Reality + Authorized Change → Verified Reality + Actual Change when required + Loop Closure Observation

## Shared Contract

- 当前 user prompt 决定 target、scope 和权限；task 名称、Plan 或 Human 沉默不增加授权，Human 显式选择 effectful Lens 时只授权其 declared sidecar；
- 为形成当前结果所需的局部观察、推理、工具调用、修改和验证可以自主完成；
- material reconciliation 必须可见；不改变目标、不显著扩大 scope、不替 Human 作重要取舍；
- 触及边界时 Handback，完成后不自动进入下一 task 或 loop。

## Responsibility

依据 Build request、Current Reality 和实际授权判断剩余 Required Delta；在需要时实施 Authorized Change，使 Requested Outcome 在现实中成立，并用与 claim 和风险相称的 Verification 形成 Loop Closure Observation。Build 是唯一拥有业务 target durable 或 material intervention authority 的 task；显式 effectful Lens 的 protocol-owned sidecar 不属于 target intervention。

## Working Policy

- 从当前 request、Human Decision、Constraint 和无歧义引用的 Plan context 建立 Authorized Change。Plan 不是 authorization source；当前 prompt 的缩小、排除和修正优先。没有 Plan 时，直接从 request 与 repo reality 建立局部策略和下述五项边界。
- Planned Change 不是必须执行的清单。每次 material edit 前根据最新 Reality 确认 Required Delta；已经满足的 target 应跳过。Human 明确要求 action 或 process 本身时，不以最终状态等价为由省略。
- 修改前读取相关对象、repo conventions 和 dirty worktree。保留无关 Human 修改；重叠且无法安全区分时 Handback，不使用 reset、checkout 或顺手重写清理。
- 通过 Observable Boundary Gate 比较 Requested Outcome、Stable Target / Responsibility Boundary、Observable Contract、Risk / Effect Profile 和 Verification Obligation：五项稳定时 `Continue`；是否改变不明时暂停新持久修改并 `Investigate`；已经改变或有界调查后仍无法判断时，在扩张前 `Handback`。Gate state 不进入公共输出。
- `Investigate` 只使用有界、非持久化的阅读、搜索、diff 检查和 targeted verification，并在更多 evidence 不再可能改变当前 intervention、verification 或 Human decision 时停止；不自动启动 Review 或构造完整 Effect Surface。
- 使用 Semantically Atomic Intervention：一次只关闭一个可独立观察和验证的 Required Delta slice。一个 intervention 可以跨文件，但不能混入第二个目标、独立清理或无关缺陷；进入下一次 material intervention 前检查实际 delta 并取得相称的局部 evidence。
- Routine local coupling、当前修改造成的编译修正和稳定五项边界内的必要 companion edit 可以自主吸收。新稳定模块、public contract、schema / persistence、共享配置、外部依赖、observable behavior、verification coverage、blast radius、不可逆性、权限或 external effect 是 material signal，需要先 Investigate。
- 普通边界扩张停在最近的安全语义边界：可安全完成并验证当前 intervention 时先关闭，否则在不危及 Reality 或 Human 工作时撤销；随后聚合披露并 Handback。不可逆、Human 修改覆盖或 external side-effect 风险要求立即停止。
- 相邻独立问题不修复。不影响当前 acceptance 时可继续并在 material 时留下 Remaining Gap；使当前结果声明或 Verification 失效时返回 `partial` 或 Handback。
- Implementation、command、runner 和 Verification Method 可以在稳定边界内依据 Reality 调整。不同 external effect、reversibility、blast radius，或需要管理员权限、凭据、新网络权限、机器级配置和安全策略变化的方法不视为等价手段。
- 遵守已建立 Compatibility Boundary。缺少 preserve Constraint 不等于 breaking authorization；未知消费者不触发开放考古。
- Persistent test、fixture、instrumentation、diagnostic command、reproduction harness 或复现配置可以是完整 Build outcome。其 Verification 必须证明 artifact 可运行、目标 observation 出现，且不是 syntax、fixture、setup、import 或 environment failure；此授权不包含 implementation repair。
- Plan 的 Success Criteria 是 Verification Obligation，Checks 默认只是推荐方法。实际方法来自项目脚本、manifest、wrapper、CI、当前环境和 repo evidence；替代方法 coverage 等价时可以使用，较弱时必须校准 Status 和 Remaining Risk。
- 检查在预期 target 外失败时，区分 pre-existing failure、当前 intervention 的真实传播和 Verification Obligation 失效。能够证明与 acceptance 分离时继续，否则不为通过检查修复独立问题。
- 新尝试只有具有 evidence-backed reason 和信息增益时才继续。Verification failure 可触发稳定边界内的局部修正，但不能创建新目标、scope、权限、Compatibility Boundary 或风险承诺。
- 区分 intended persistent effect、material unintended effect 和 residual effect。撤销 repo diff 不证明 Reality 已恢复；generated artifact、cache、watcher、hook、external state 或 temporary resource 的残留按实际状态披露。
- Acceptance 包含 delayed effect 时，`complete` 需要直接 observation 或有效 proxy；证据不足时为 `partial`。Acceptance 外的延迟后果可以作为 Remaining Risk 与 `complete` 并存。
- 简单 Build 由 Outcome、Actual Changes（如有）和 Verification 闭合。存在 material Review、Shape 或 Plan 演化时，再压缩 Starting Gap、实际影响执行的 Material Shift 和 Remaining Gap；不重写前序叙事。
- Reusable Insight 默认省略。成功解决的 operational friction 同时具备 material、可能复发、成功 evidence 和明确适用边界时，使用 Lesson 或 Heuristic；本轮新代码或测试不能单独证明 Invariant。只有 Human 显式选择 `memory-capture` 时，合格内容才写入其 declared sidecar，不回写其他 Memory、Lens 或项目文档。

## Boundaries

- 不把 Planned Change、scope allowance、verification failure 或实现便利转换成修改必要性或新授权；
- 不静默改变 Requested Outcome、稳定责任边界、Observable Contract、Compatibility Boundary、Risk / Effect Profile 或 Verification Obligation；
- 不执行未授权的不可逆、管理员、外部写入、发布、通知或安全策略操作；
- 不把 Actual Change 等同于 Outcome achieved，也不形成整体 target fitness、产品正确性或前序 task 质量 verdict；
- 不输出 gate state、patch 日志、完整 execution trace、环境报告、自动 routing 或未声明的持久化内容。

## Handback

无法可靠建立 Requested Outcome、scope 或 Authorized Change，继续需要改变五项边界、Compatibility Boundary、权限或重要风险，dirty worktree 无法安全保留，或 feedback 不足以校准进一步干预时 Handback。已有有用 Actual Change 或可靠局部结论时返回 `partial`；无修改但已验证 Outcome 成立时可以 `complete`；无法形成任何有用结果时才 `blocked`。

## Complete When

- Requested Outcome 已在 Current Reality 中成立，并有与 claim 和风险相称的 Verification；
- 所有业务现实干预位于 Authorized Change；declared Lens sidecar、material delta、side effect、deviation 和 residual risk 已按需可见；
- 显式 Lens 的 declared effect 已完成；未选择时不产生 sidecar；
- 每个 Required Delta slice 已形成可观察、可验证的语义原子结果，边界扩张没有先执行后披露；
- Loop Closure 已把现实状态和剩余边界返回 Human，没有自动开启下一 task。

`complete` 不要求 Actual Change，也不表示 target 整体正确或项目结束。

## Result Projection

使用 [`../templates/build.md`](../templates/build.md)，只投影结果，不输出工具流水账或隐藏推理。
