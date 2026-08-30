# Human ReAct Tasks

本目录定义 Human ReAct 第一版四个顶层 task。本文是 task 体系的唯一总说明，负责共同规则、详细定位、操作边界与完成条件。`review` 已有第一版 task prompt；其余 task-specific 文件仍为空。

正式 task：

```text
review / shape / plan / build
```

`explore` 不再是顶层 task。搜索、阅读、追踪、试验和更新理解是所有 task 都可以按需使用的内部能力。

目录内容：

- [`review.md`](review.md)
- [`shape.md`](shape.md)
- [`plan.md`](plan.md)
- [`build.md`](build.md)
- [Review、Gap Analysis 与 Diagnosis](review-and-diagnosis.md)

## 总体模型

四个 task 分别维护不同状态：

| Task | 状态转换 | 核心问题 |
| --- | --- | --- |
| `review` | Existing Target → Evidence Context | 现状、问题、产物或当前判断意味着什么？ |
| `shape` | Human Context + Decisions → Intent Model | 我们真正想让什么成立？ |
| `plan` | Intent + Evidence + Constraints → Execution Model | 已稳定的 intent 应该怎样实施？ |
| `build` | Authorized Intent / Plan → Reality + Verification | 怎样在授权范围内把目标变成现实？ |

Task 可以被直接选择、跳过或重复。选择 task 不证明前序 task 已完成，也不自动扩大 user prompt 的授权。

## Task Prompt Contract

Task prompt 是 Agent 的本轮行为契约，不是 user input form、固定执行流程或输出模板。它只负责定义：

- 本轮需要产生的状态转换；
- Agent 在当前 task 内可以自主完成的工作；
- 不得跨越的目标、scope、权限和外部效果边界；
- 必须 Handback 的条件；
- 可观察的完成条件。

Task prompt 与 Result Template 的关系是：

```text
User Prompt + Conversation
+ Task Prompt             behavior and authority boundary
        ↓
Agent Micro ReAct
        ↓
Result Template           chat projection only
```

Task 文件不得复制公共 Result Packet。输出字段、条件 section 和表达规则统一由 [`templates/**`](../templates/) 负责。

### Minimum Common Template

四个 task-specific 文件统一使用以下最小骨架：

```markdown
# Task: <name>

> <Input State> → <Result State>

## Shared Contract

- 当前 user prompt 决定实际 target、scope 和授权；
- task 名称只决定本轮结果类型，不提供额外权限；
- 为形成当前结果所需的局部观察、推理、工具调用和验证可以自主完成；
- 不改变目标、不显著扩大 scope、不替 Human 作出重要取舍；
- 触及边界时 Handback，不静默切换 task；
- 完成后执行内部 Closure Check，但不输出必填 Reflection。

## Responsibility

<!-- 本 task 唯一负责产生什么结果。 -->

## Working Policy

<!-- 必须完成的语义工作、允许吸收的内部操作和自适应原则；不规定固定步骤。 -->

## Boundaries

<!-- 不负责的结果、禁止的外部效果和不得推断的授权。 -->

## Handback

<!-- 哪些目标、scope、权限、风险或 Human-owned choice 要求停止。 -->

## Complete When

<!-- 哪些可观察语义条件成立时，本 task 可以结束。 -->

## Result Projection

使用 `../templates/<task>.md`。
只投影 Task Result，不输出内部推理或工具流水账。
```

`Shared Contract` 是有意保留的最小重复，使单个 task 文件被现代 harness 独立加入 context 时仍能保持核心边界。其余 section 只写 task-specific delta，不复述本 README、宏观 loop 或 Project Context Layer。

### Section Semantics

- `Responsibility` 定义唯一结果，不承担 task routing；
- `Working Policy` 描述证据、决策和适应原则，不规定工具调用顺序；
- `Boundaries` 说明当前 task 不拥有的结果与外部效果；
- `Handback` 只列真正要求停止 micro ReAct 的边界事件；
- `Complete When` 使用语义结果而不是“读过文件、跑过命令、填满 section”等过程条件；
- `Result Projection` 只引用对应 template，不复制字段。

### Prompt Design Boundaries

Task prompt 默认直接使用当前 user prompt 与 conversation，不要求 Human 填写 `Target`、`Mode`、`Depth`、`Thread`、`Lens` 或 `Memory` 等结构化 intake。请求清晰时直接工作；缺失信息能在现有 scope 与权限内安全补足时自主补足；只有会实质改变结果时才 Handback。

第一版 task prompt 不包含：

- 大段 `Use When / Do Not Use When` 路由表；
- front matter、mode 或 artifact taxonomy；
- 固定的 Explore → Analyze → Reflect → Plan 步骤；
- 完整 self-audit checklist、persona 或大量示例；
- 强制的下一 task 建议、routing table 或自动转换规则；
- Memory/Lens loading、binding 或自动写入；
- 公共输出骨架和 task-specific Result Template 内容。

示例与评测案例未来应放在 prompt 之外，用来验证 task 是否稳定，而不是成为默认 context。

## Established / Conditional Separation

Task 结果需要区分当前已成立或已承诺的状态，与依赖未确认前提、候选选择或未来条件的推演。条件推演不得自动更新 task 所维护的状态，也不构成 Human decision 或执行授权。

`Assumption` 与 `Conditional` 不同：

- `Assumption` 是为继续当前工作而暂时采用的未验证前提；
- `Conditional` 不接受该前提，只说明“如果它成立，会推出什么”。

该分离在不同 task 中的候选映射为：

| Task | Established / Committed State | Conditional Projection | 晋升条件 |
| --- | --- | --- | --- |
| `review` | Evidence-backed Finding / Diagnosis | Premise-Conditional Analysis | 新 evidence 确认 premise |
| `shape` | Current Intent / Human Decision | Candidate Direction / Conditional Intent | Human 明确选择或确认 |
| `plan` | Chosen Approach / Execution Model | Contingency / Conditional Branch | 条件被验证，或所需 Human 取舍已完成 |
| `build` | Actual Change / Verification | Possible Fix / Future Change | Human 新授权后实际执行并验证 |

第一版只在 `review` 中实现 Premise-Conditional Analysis。其余行是设计记录，不表示 `shape / plan / build` 已具备对应行为，也不改变上述公共 Shared Contract。

## Task Closure

每个 task 在 Handback 前都应进行轻量的内部 Closure Check：比较 requested outcome 与 actual outcome，确认已完成和已验证的部分，识别未完成、跳过或偏离的内容，并判断本轮是否产生了真正可复用的新认识。

Closure Check 不是新的 task、公开 checklist 或必填 `Reflection` 字段。它只用于校准 `Status`、结果内容和 Human Attention；没有对 Human 判断有价值的信息时不单独投影。需要对产物进行更深入、独立的事后评价时，仍由 Human 另行发起 review。

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

Gap analysis 与 diagnosis 的概念边界及组合方式见 [Review、Gap Analysis 与 Diagnosis](review-and-diagnosis.md)。

### 边界

Review 不负责：

- 静默改变 Human 的目标或偏好；
- 决定新的产品方向或关键取舍；
- 输出完整实施方案；
- 修复被分析对象；
- 把被审计 user prompt 中的执行请求自动视为授权；
- 自动进入 shape、plan 或 build。

Review 可以在有信息价值时给出少量、证据驱动的 Follow-up Options，帮助 Human 选择新的探索或决策方向。它们不是 task routing，不构成授权，也不会被 Agent 自动执行。

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

Shape 可以在多轮 user prompt 中重复进行。下式描述 Agent 内部维护的 intent，而不是要求每轮输出完整快照：

```text
Current Intent
+ New Human Context
+ New Human Decisions
- Invalidated Assumptions
= Updated Intent
```

每轮都应识别新增 context 的类型，将其合并进当前 intent，并检查与已有内容的冲突。连续对话本身是 working context；Shape 负责维护当前理解，但不是逐轮对话摘要器，也不维护独立 memory artifact。

Shape 的 delta-first、summary-on-demand 输出规则由 [Templates README](../templates/README.md) 定义。`complete` 只表示本轮 intent 已收敛到预期程度，不会自动启动下一 task。

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

Plan 在内部从当前 conversation 形成与执行相关的 planning basis：

```text
Goal
In Scope / Out of Scope
Relevant Facts
Constraints
Decisions
Assumptions
```

Plan 不机械拼接历史。对于相互矛盾或已经过时的 context，应明确来源和影响；具体投影规则由 [Templates README](../templates/README.md) 定义。

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

### 实施收尾

Build 在 Handback 前必须区分三类内容：

1. `Actual Changes`：现实中已经发生了什么；
2. `Incomplete / Deviated`：哪些未完成、跳过、阻塞或偏离原策略；
3. `Reusable Insight`：本轮是否产生了将来可能复用的新经验。

前两类属于执行核对，在相关时必须明确披露。第三类始终可选，只保留有本轮证据支持、适用范围明确且会改变未来行动的认识。单次案例只能支持 heuristic 时，应明确其有限证据，不能升级为 project invariant。

Reusable Insight 不会自动更新项目文档、Memory 或 Project Lens。Human 可以在后续重复验证后另行决定是否沉淀；当前 task 不执行知识晋升。

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
- 有真实新经验时已按证据强度区分 invariant、lesson 或 heuristic；
- 控制权已经 Handback 给 Human。

## Shared Autonomy And Handback

如果当前 task 内缺少的信息可以在既定目标、scope 和权限内安全补足，Agent 应自行完成必要的局部观察、搜索、diagnosis、规划或验证。出现以下情况时必须 Handback：

- 需要改变 Human 目标或关键产品语义；
- 需要显著扩大 scope；
- 需要新的权限、风险承诺或不可逆操作授权；
- 存在会实质改变结果、且上下文无法确定的 Human-owned choice；
- 当前 task 的预期结果与 user prompt 无法安全调和。

Task 之间的反馈关系、同-task 多轮收敛和宏观 ReAct 对应统一见 [Loop](../loop.md)，不在本文重复定义。
