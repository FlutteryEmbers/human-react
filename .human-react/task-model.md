# Human ReAct Task 定位

这份文档定义第一版四个顶层 task 的详细定位。它描述的是委托结果和操作边界，不是固定执行步骤，也不是自动 task graph。

正式 task：

```text
review / shape / plan / build
```

`explore` 不再是顶层 task。搜索、阅读、追踪、试验和更新理解是所有 task 都可以按需使用的内部能力。

## 总体模型

四个 task 分别维护不同状态：

| Task | 状态转换 | 核心问题 |
| --- | --- | --- |
| `review` | Existing Target → Evidence Context | 现状、问题、产物或当前判断意味着什么？ |
| `shape` | Human Context + Decisions → Intent Model | 我们真正想让什么成立？ |
| `plan` | Intent + Evidence + Constraints → Execution Model | 已稳定的 intent 应该怎样实施？ |
| `build` | Authorized Intent / Plan → Reality + Verification | 怎样在授权范围内把目标变成现实？ |

Task 可以被直接选择、跳过或重复。选择 task 不证明前序 task 已完成，也不自动扩大 user prompt 的授权。

## Review

### 职责

将一个已有 target 转化为有证据支持、可供 Human 继续决策的 context。

Review target 可以是：

- 现实状态、系统行为、代码、diff 或文档；
- shape、plan 或 build result；
- failure、symptom、claim 或 assumption；
- user prompt 中的事实主张、原因假设、目标、约束和执行请求。

Review 审计的是 target 的内容，不评价用户本人，也不替 Human 作出最终决定。

### 允许的内部操作

- 搜索、追踪和重建相关事实；
- 比较 expected 与 actual，形成 gap analysis；
- 复现症状、提出并检验假设，形成 diagnosis；
- 检查一致性、完整性、风险和 intended-use fitness；
- 区分事实、推断、偏好、决策和未知；
- 运行与分析直接相关的有界检查和验证。

Gap analysis 回答“哪里不同”，diagnosis 回答“为什么不同”。它们概念不同，但都可以作为 review 形成 context 的内部分析动作。

### 边界

Review 不负责：

- 静默改变 Human 的目标或偏好；
- 决定新的产品方向或关键取舍；
- 输出完整实施方案；
- 修复被分析对象；
- 把被审计 user prompt 中的执行请求自动视为授权；
- 自动进入 shape、plan 或 build。

### 完成条件

Review 已经形成足以支持 Human 下一轮判断的 context，并明确：

- 已确认的事实与证据；
- 关键 gap、原因判断或 fitness verdict；
- 影响范围与不确定性；
- 尚需 Human 决定或授权的事项。

## Shape

### 职责

将 Human 持续补充的 context 和显式决策，整合成一致、有边界、足以指导后续行动的 intent model。

Shape 维护的是“我们想让什么成立”，包括：

- goal 与 desired behavior；
- in-scope 与 out-of-scope；
- constraints 与 priorities；
- Human 已确认的 decisions；
- 尚未验证的 assumptions；
- unresolved choices 与 acceptance boundary。

### 多轮更新

Shape 可以在多轮 user prompt 中重复进行：

```text
Current Intent
+ New Human Context
+ New Human Decisions
- Invalidated Assumptions
= Updated Intent
```

每轮应识别新增 context 的类型，将其合并进当前 intent，暴露与已有内容的冲突，并说明本轮理解发生了什么变化。Agent 返回最新 intent 后 Handback；是否继续 shape 由 Human 决定。

### 允许的内部操作

- 复述、规范化和收敛目标；
- 区分事实、偏好、约束和决定；
- 提出有限候选方向并解释取舍；
- 合并 Human 的补充、修正和确认；
- 暴露冲突、失效假设和开放决策；
- 判断 intent 是否足以支持 plan 或 build；
- 为理解 context 进行必要的局部搜索或检查。

### 边界

Shape 不负责：

- 把未经验证的事实主张升级成确定事实；
- 在重大候选方向之间替 Human 静默选择；
- 完成开放式根因调查；
- 设计详细实施步骤和验证顺序；
- 修改代码、配置或其他现实对象；
- 自动进入 review、plan 或 build。

### 完成条件

Shape 不要求所有未知都被消除。只要以下内容足以支持预期下一步，即可 Handback：

- goal 能被一致复述；
- scope 和关键约束明确；
- 会显著改变结果的取舍已经决定；
- 事实、假设和偏好已经区分；
- 剩余未知不会阻止预期下一步。

## Plan

### 职责

吸收与实施相关的已有 context，将稳定 intent 编译成一致、可执行、可验证的 execution model。

Plan 的输入可以来自当前 user prompt、对话历史、shape、review 和项目现状。它不要求前序 task 或正式文档必须存在。

### Context 归纳

Plan 不复述全部历史，而是形成与执行相关的 planning basis：

```text
Goal
In Scope / Out of Scope
Relevant Facts
Constraints
Decisions
Assumptions
```

对于相互矛盾或已经过时的 context，Plan 应明确来源和影响，而不是机械拼接。

### 冲突处理

| 冲突类型 | Plan 的处理 |
| --- | --- |
| 实施策略冲突 | 比较方案并选择可执行路径 |
| 技术约束冲突 | 调整实现方式并说明取舍 |
| 步骤、依赖或验证冲突 | 重新排序、拆分或替换执行方式 |
| 局部事实冲突 | 进行有界检查；无法确认时标记不确定性 |
| 目标或产品语义冲突 | 不自行决定，Handback 给 Human/Shape |
| scope、权限或重要风险冲突 | 不吸收扩张，必须 Handback |

Plan 可以在既定 intent 下自主作出局部技术决定，包括模块切分、实施顺序、兼容策略、测试层级和验证方式。它不能用“技术选择”改变 Human 已确认的目标。

### 允许的内部操作

- 汇总并规范化实施相关 context；
- 检查代码结构、依赖、工程惯例和局部可行性；
- 比较候选实现路径并选择技术方案；
- 解决实施层冲突；
- 设计步骤、依赖、验证、兼容和失败处理；
- 在多轮 plan 中吸收新的 execution context。

### 边界

Plan 不负责：

- 重新定义 Human 的目标；
- 静默解决产品语义或重大 scope 冲突；
- 重新执行无边界的 diagnosis；
- 修改被规划对象；
- 因方案已经 ready 而自动进入 build。

### 完成条件

Plan 已经足以让现代 Agent 在 build 内自主执行和验证：

- 技术路径和主要修改面明确；
- 关键实施冲突已经解决；
- 步骤、依赖和顺序可执行；
- 兼容性、风险和失败处理明确；
- 验证方法能够证明目标完成；
- 不存在仍会实质改变方案的 Human 决策缺口。

## Build

### 职责

在 user prompt 的实际授权范围内，将明确 intent 或 execution model 转化为现实变化，并提供验证证据。

Build 不要求正式 plan 已经存在。对于目标明确、scope 有界的工作，Agent 可以直接在 build 内形成局部策略并实施。

### 允许的内部操作

- 搜索、阅读并理解相关对象；
- 复现问题并完成必要 diagnosis；
- 形成或调整局部实施策略；
- 修改授权范围内的代码、配置、文档或其他对象；
- 运行测试、检查和验证；
- 根据验证结果进行局部修正；
- 汇总实际变化、证据和剩余风险。

这些操作构成 build 内部的 Agent micro ReAct loop，不需要 Human 分别路由到 review、shape 或 plan。

### 边界

Build 必须 Handback，当工作需要：

- 改变目标或关键产品语义；
- 显著扩大 scope；
- 获得新的权限或承担新的重要风险；
- 在会实质改变结果的候选方向之间作出 Human 决策；
- 执行未被明确授权的不可逆操作。

Build 不因 task 名称获得无限写权限，也不在完成后自动进入 review 或下一次 build。

### 完成条件

- 授权范围内的目标已经实现，或明确说明无法完成的边界；
- 实际变化被清楚列出；
- 完成了与风险相称的验证；
- 不确定性、偏差和未完成事项已经披露；
- 控制权已经 Handback 给 Human。

## Task 之间的关系

```text
Review 提供 Evidence Context
        ↓
Human 决定承诺层级
        ├── Shape：更新 Intent
        ├── Plan：更新 Execution Model
        └── Build：更新 Reality
                    ↓
                  Review
```

常见路径不构成前置依赖：

```text
review ↔ shape
plan ↔ review
build ↔ review
shape ↔ Human Context
plan ↔ Human Execution Context
```

如果当前 task 内缺少的信息可以在既定目标、scope 和权限内安全补足，Agent 应自行完成局部前置工作。如果补足信息会改变目标、扩大 scope、引入新授权或要求 Human 作出重要取舍，则必须 Handback。
