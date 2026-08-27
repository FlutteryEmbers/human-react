# Human ReAct

Human ReAct 是一套面向现代 Agent harness 的轻量 Prompt Workspace。

它把 Human 视为宏观 ReAct loop 的中心。Human 负责维持目标、上下文和授权边界，并决定每一轮希望 Agent 产生什么结果；Agent 在该轮委托的边界内，自主进行必要的观察、推理、工具调用、执行和验证；结果返回后，由 Human 判断是否继续以及下一轮如何委托。

Human ReAct 的实际形态不是 task 的线性流程，而是一个由 Human 维持的嵌套反馈系统。常见的跨-task 路径以 `review` 为证据反馈入口：Human 通过 review 将现实、产物或自己的当前判断转化为可继续决策的 context，在 `shape`、`plan` 与 `build` 不同承诺层级之间移动，再将产生的新结果交给 review。同时，Human 也可以通过多轮 shape 持续补充 intent context，或通过多轮 plan 补充 execution context。所有循环都是 Human 的操作模型，不是系统自动执行的 task graph。

```text
Review：重建 context、检查证据并形成反馈
                  ↓
Human 判断应在哪个承诺层级继续
                  ↓
Shape / Plan / Build
                  ↓
产生新的定义、方案或现实结果
                  ↓
Review：形成下一轮 Observation
```

## 核心定位

Human ReAct 中的 task 不是 workflow stage，也不是要求 Human 判断 Agent 下一步应该采用哪种认知操作。

Task 表示的是本轮委托的结果类型和操作边界。Human 可以按照自己想获得的结果选择 task；Agent 负责在 task 内完成必要的局部探索、推理、规划、工具调用和验证。选择 `build` 不表示此前必须执行过 `shape` 或 `plan`，也不表示 Agent 可以脱离 user prompt 获得无限制的修改授权。

在宏观循环中，`review` 主要承担 Observation / Evaluation，`shape` 与 `plan` 主要承担不同层级的 Reason / Commitment Formation，`build` 承担对现实产生效果的 Action。Human 负责解释 review 产生的反馈，并决定保持当前层级、回退到更高层级，还是进入实际执行。

因此，Human 负责：

- 给出目标、上下文和本轮意图；
- 决定允许产生哪类外部效果；
- 处理需要扩大范围、改变目标或作出重要取舍的边界事件；
- 解释 Agent 返回的结果并发起下一轮委托。

Human 不需要：

- 精确判断 Agent 当前处于哪个内部推理阶段；
- 手动调度 Agent 的每一次观察、思考或工具调用；
- 按固定顺序依次执行所有 task；
- 为一次 `build` 预先准备完整的探索文档或计划文档。

## Tasks

第一版保留四种委托模式：

| Task | Human 想解决的问题 | 本轮主要承诺 |
| --- | --- | --- |
| [`review`](.human-react/tasks/review.md) | 现状、问题、产物或当前判断意味着什么？ | 将已有 target 转化为 evidence context，不修改被分析对象 |
| [`shape`](.human-react/tasks/shape.md) | 我们真正想让什么成立？ | 将 Human context 和显式决策收敛成有边界的 intent model |
| [`plan`](.human-react/tasks/plan.md) | 已稳定的 intent 应该怎样实施？ | 吸收相关 context、解决实施层冲突，形成可执行且可验证的 execution model |
| [`build`](.human-react/tasks/build.md) | 请把这个目标实现出来 | 在实际授权范围内将 intent 或 plan 转化为现实并验证结果 |

这些 task 是彼此独立的委托入口，不是生命周期阶段。Human 可以自由选择、跳过和重复调用，例如：

```text
build

review → shape → review → build

build → review → plan → build
```

箭头只表示 Human 实际发起了下一轮 user prompt，不表示系统会自动转换 task。

## 实际循环

实际使用包含跨-task 循环和同-task 多轮循环。

常见的跨-task 循环包括：

1. `review ↔ shape`：让对现实的评价与目标、边界和取舍逐步收敛；
2. `plan ↔ review`：检查执行方案是否足够完整，必要时回到 `shape` 修正更高层问题；
3. `build ↔ review`：检查现实变化是否符合承诺，并根据问题层级决定继续 build、回到 plan 或回到 shape。

同一个 task 也可以由 Human 重复发起：

1. `Human Context ↔ shape`：Human 持续补充事实、偏好、约束和决策，shape 维护最新 intent；
2. `Human Execution Context ↔ plan`：Human 持续补充技术约束或执行决策，plan 维护最新 execution model。

可以概括为：

```text
Review
→ (Shape → Review)*
→ Plan
→ (Review → Shape/Plan)*
→ Build
→ Review
```

这里的 `*` 只表示 Human 可以重复发起该类委托。完整模型、Mermaid 图和各条反馈路径见[实际循环说明](.human-react/loop.md)。

## 四种状态转换

四个 task 可以用各自维护的状态来区分：

```text
Review：Existing Target → Evidence Context
Shape：Human Context + Decisions → Intent Model
Plan：Intent + Evidence + Constraints → Execution Model
Build：Authorized Intent / Plan → Reality + Verification
```

Shape 可以整合多轮 Human context、暴露冲突和提出候选取舍，但不能把未经确认的重大选择静默写入 intent。Plan 可以归纳前序 context、验证局部实施前提并解决实施策略、步骤、依赖和验证之间的冲突；如果冲突会改变目标、产品语义、scope 或授权，则必须 Handback，而不能在 plan 内自行吸收。

各 task 的详细职责、允许的内部操作、完成条件与 Handback 边界见 [Task 定位](.human-react/task-model.md)。

## Review：统一的 Observation 入口

`review` 的单一职责不是只给出 verdict，也不是只做 gap analysis，而是：

> 将一个已有 target 转化为有证据支持、可供 Human 继续决策的 context。

Review target 可以是现实状态、行为、代码或 diff、shape、plan、build result，也可以是 user prompt 中的事实主张、原因假设、目标、约束和执行请求。Review 审计的是这些内容，不是评价用户本人，也不取代 Human 的最终决策权。

为了形成所需 context，Agent 可以在 review 内部自主使用不同分析动作：

```text
Explore：搜索、追踪并重建相关事实
Gap Analysis：比较 expected 与 actual
Diagnosis：解释已知 gap 或症状为什么发生
Assessment：判断 target 能否用于预期目的
```

这些动作概念上不同，但共享相同的本轮终点和外部效果边界，因此暂不拆成顶层 task。`explore` 被视为所有 task 都可使用的内部能力，不再要求 Human 单独选择；diagnosis 可以作为 review 的内部分析过程，但定位到原因后不得自动设计或实施修复。详细说明见 [Review、Gap Analysis 与 Diagnosis Note](.human-react/review-and-diagnosis.md)。

Review 可以暴露下一轮可能需要处理的层级，但不得自动进入 `shape`、`plan` 或 `build`。四个正式 task 文件当前均为空，只锁定最小目录与命名；具体 prompt 行为将在这一定位下继续设计。

## 自治与 Handback

Agent 的 task 内自治可以包含多个内部操作。例如一次 `build` 可以先读取代码、理解局部结构、形成实现策略、修改、测试并根据结果调整，而不需要 Human 把这些内部操作分别路由到其他 task。

当缺失的信息或前置工作可以在当前目标和权限内安全补足时，Agent 应在当前 task 内继续。遇到以下情况时，Agent 应停止并 Handback 给 Human：

- 需要改变 Human 给出的目标或关键产品语义；
- 需要显著扩大 scope；
- 需要新的权限、风险承诺或不可逆操作授权；
- 存在会实质改变结果的关键取舍，且无法从上下文确定；
- 当前 task 的结果类型与 user prompt 发生无法安全吸收的冲突。

Agent 可以说明边界和可能的后续委托，但不得自动启动下一 task。

## 最小不变量

- Human 维持宏观 ReAct loop；
- task 定义本轮委托模式，不定义固定流程阶段；
- Agent 可以在 task 内自主运行多轮局部 ReAct loop；
- task 之间没有前置依赖或自动转换；
- task 名称、建议和前序结果本身不构成执行授权；
- scope expansion、目标改变和新的重要授权必须 Handback；
- 每轮结束后由 Human 决定是否以及如何继续。

Human ReAct 不是自动 task graph、Agent orchestration 平台或文档维护流程。它不负责持久化 session、同步项目文档、晋升知识或归档工作项。

## 目录

```text
.human-react/
├── README.md
├── loop.md
├── review-and-diagnosis.md
├── task-model.md
├── tasks/
    ├── shape.md
    ├── plan.md
    ├── build.md
    └── review.md
└── templates/
    ├── README.md
    ├── shape.md
    ├── plan.md
    ├── build.md
    └── review.md
```

`.human-react/**` 是可嵌入宿主项目的轻量 Prompt Workspace。[`templates/**`](.human-react/templates/) 只定义 task 结果的 chat projection，不保存实际输出或运行状态。当前不包含 Copilot commands、Codex skills、roles、lenses、session、memory、运行期文档维护体系、schema、生成器或安装器。

## 与 Workflow Lite 的关系

[`sandbox/workflow`](sandbox/workflow/) 中的 Workflow Lite 是结构与设计思路参考。Human ReAct 借鉴显式 task、单 task prompt 和适配现代 harness 的方向，但不是 Workflow Lite 的兼容版本，也不继承它的文档维护、artifact、session、persist、sync 或 archive 体系。

Human ReAct 不把 Workflow Lite 的 task taxonomy 当作必须由 Human 准确执行的流程。它的核心差异是：

> Human 维护目标、context、授权和反馈循环；task 表达本轮希望更新的状态，Agent 负责委托内部的微观 ReAct loop。
