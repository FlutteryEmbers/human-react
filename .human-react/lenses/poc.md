---
id: poc
type: posture
activation: explicit-only
applies_to:
  - review
  - shape
  - plan
  - build
---

# Proof Of Concept

## Purpose

以 Human 明确选择的 PoC posture 和 Demo Contract 收敛当前 task，只要求最小可信 happy path，同时保留核心路径 correctness、可验证性和明确的升级边界。

## Use When

Human 在 Review、Shape、Plan 或 Build 中显式选择本 Lens，且当前 prompt 或已确认 context 提供了可判断的 Demo Contract 时使用。PoC 表示交付姿态，不表示可以忽略 task contract、真实风险或核心行为正确性。

## Attention

- `required-now`：Demo Contract 成立不可缺少的行为；
- `allowed simplifications`：当前受控条件下可以接受的简化；
- `explicitly deferred`：本轮不要求、且不妨碍 Demo Contract 的工作；
- `upgrade triggers`：一旦出现就必须重新评估 PoC 边界的现实条件。

在不同 task 中：

- Review 按明确 Demo Contract 判断 fitness，不以未要求的 production completeness 制造 gap；
- Shape 收敛 smallest credible happy path 和 allowed simplifications；
- Plan 避免推测性抽象、兼容层和未触发的扩展点，只纳入关闭 required-now delta 必需的修改；
- Build 实施并验证 required-now behavior；Lens 本身不产生 Build authorization。

## Evidence Expectations

- Demo Contract、受控输入和成功条件必须能够被观察或验证；
- 每个 simplification 都有当前成立的边界，而不是把未知当作安全；
- 核心 happy path 的 correctness 不因 PoC posture 降级；
- uncontrolled input、真实 external consumer、持久化兼容义务、安全边界或其他 upgrade trigger 必须可见。

## Fallback

Demo Contract 不完整但仍可形成有用结果时，只采用 prompt 已明确支持的 working basis，并标记影响判断的未知；缺口会实质改变 required-now、风险或验收时，按当前 task Handback。发现 upgrade trigger 时停止依赖相应 simplification，暴露需要 Human 决定的新边界，不自动切换为其他 posture。

## Stop Conditions

- 当前 task 已围绕 Demo Contract 形成其应有结果；
- 继续工作只会增加未触发的 production hardening、扩展点或兼容层；
- 现实条件越出受控 PoC 边界，或继续需要新的 scope、权限、风险接受或外部效果授权。

## Boundaries

本 Lens 不把“原型”变成低质量豁免，不隐藏已知风险，不推断未来消费者，也不激活 production 或 migration Lens。它不改变当前 task 的输出、Status、Handback 或权限；尤其不会把 Plan、Demo Contract 或文件存在解释为 Build authorization。
