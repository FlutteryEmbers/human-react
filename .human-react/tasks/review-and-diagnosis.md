# Review、Gap Analysis 与 Diagnosis

这份 note 记录 `review`、gap analysis 与 diagnosis 的概念边界，以及它们为什么可以被同一个顶层 `review` task 吸收。

## 核心区别

```text
Review / Gap Analysis
对象是否满足预期？哪里不符合？影响是什么？

Diagnosis
已知差距为什么发生？什么因果机制产生了当前症状？
```

Gap analysis 建立 expected 与 actual 之间的差异；diagnosis 从这个差异出发，通过复现、证据收集、假设检验和因果定位解释其来源。Diagnosis 依赖 gap，但不等同于 gap analysis。概念上的不同不要求它们成为不同的顶层 task。

## Review

Review 的单一职责是把一个已有 target 转化为有证据支持、可供 Human 继续决策的 context。Target 可以是现实状态、行为、代码或 diff、shape、plan、build result，也可以是 user prompt 中的主张、假设、目标、约束或执行请求。

为了形成这个 context，review 可以：

- 搜索、追踪并重建相关事实；
- 描述 expected 与 actual；
- 标记 gap、影响和不确定性；
- 通过假设检验定位已知 gap 的原因；
- 判断对象是否满足预期用途；
- 将事实、推断、偏好、决策和未决问题分开表达。

Review 审计的是 target 的内容，而不是用户本人。它可以指出用户陈述中的证据缺口、内部冲突、原因假设和隐含风险，但不能把用户偏好当作事实错误，也不能替 Human 改变目标或作出关键取舍。

## Diagnosis

Diagnosis 的核心输入是已经观察到的 failure、symptom 或 gap。它负责：

- 确认或复现症状；
- 收集与问题直接相关的 context；
- 提出、检验并排除原因假设；
- 给出有证据支持的直接原因、根因和置信度；
- 明确尚未解释的部分。

Diagnosis 的终点是因果解释，不是修复方案或实际修改。在当前模型中，它是 review 为形成 problem context 可以使用的内部分析过程。需要设计修复方向时由 Human 进入 `shape` 或 `plan`；需要直接修复时进入 `build`；修复后的结果可以再次交给 `review`。

## 与现有 Tasks 的关系

```text
Review：形成关于已有 target 的 evidence-backed context。
Shape：根据 context 收敛目标、边界和取舍。
Plan：把稳定意图转化为可执行方案。
Build：改变现实并验证结果，可在内部完成必要诊断。
```

`explore` 不再是顶层 task。搜索、追踪、阅读和开放假设空间是 review、shape、plan 与 build 都可以使用的内部能力。如果 Human 只要求找出原因并为后续提供 context，可以选择 review；如果 Human 已授权解决问题，可以直接选择 build，让 Agent 在 build 的局部 ReAct loop 内完成复现、诊断、修改和验证。

## 同一 Review 中的不同分析动作

一次 review 可以按问题需要组合以下动作：

```text
Explore：获得相关事实和调用链 context
Gap Analysis：比较当前结果与预期结果
Diagnosis：解释差距的因果来源
Assessment：判断结果能否用于预期目的
```

这些内部动作不需要 Human 逐一选择。Review 的边界由本轮终点保持一致：形成 context，而不是改变目标、制定完整实施方案或修改现实。无论 review 是否完成 diagnosis，Human 都保留是否进入 shape、plan 或 build 的决定权。
