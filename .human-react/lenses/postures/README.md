# Posture Lens Reservation

本目录预留跨多个 task 持续生效的 Posture Lens，目前不包含正式 Lens。

Posture Lens 将 Project Profile 中由 Human 确认的交付姿态解释成 task 内行为。候选示例包括 `poc`、`production` 或 `migration-safe`。它不保存项目当前处于哪种 posture；状态与 scope 属于 Memory 中的 Project Profile。

以候选 `poc` 为例，Lens 可以解释：

- Review 按明确 Demo Contract 判断 fitness，不以未要求的生产完整度制造 gap；
- Shape 收敛 smallest credible happy path 与 allowed simplifications；
- Plan 避免推测性抽象、兼容层和未触发的扩展点；
- Build 只实现 required-now behavior，并验证 Demo Contract；
- 发现 uncontrolled input、真实 external consumer 或其他 upgrade trigger 时 Handback。

PoC 不代表可以忽略核心路径 correctness。一个候选 Posture Lens 必须区分 required-now、allowed simplifications、explicitly deferred 和 upgrade triggers，并且不能扩大 scope、权限或风险授权。

现阶段没有 `poc` 或其他正式 Posture Lens，也没有 Project Profile binding 或自动激活。
