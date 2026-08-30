# Human ReAct

Human ReAct 是一套面向现代 Agent harness 的轻量 Prompt Workspace。它把 Human 视为宏观 ReAct loop 的中心：Human 维持目标、上下文和授权边界，选择本轮希望得到的结果；Agent 在该委托内自主观察、推理、调用工具、执行和验证，然后 Handback。

它不是线性 workflow、自动 task graph、Agent orchestration 平台或文档维护系统。

## Operating Model

```text
Review：把现实、产物或当前判断转化为 Evidence Context
                         ↓
Human：解释反馈并选择下一轮承诺层级
                         ↓
Shape / Plan / Build：更新 Intent、Execution Model 或 Reality
                         ↓
Human 决定是否再次 Review、重复当前 task 或改选其他 task
```

Task 表示本轮委托的结果类型和操作边界，不表示固定流程阶段，也不要求 Human 调度 Agent 的内部认知步骤。搜索、追踪、diagnosis、局部规划和验证都是 Agent 可以在当前 task 内按需使用的能力。

## Tasks

第一版保留四种彼此独立的委托模式：

| Task | Human 想获得的结果 | 状态转换 |
| --- | --- | --- |
| `review` | 对现状、问题、产物或判断形成 evidence-backed context | Existing Target → Evidence Context |
| `shape` | 收敛我们真正想让什么成立 | Human Context + Decisions → Intent Model |
| `plan` | 把稳定 intent 编译成可执行、可验证的方案 | Intent + Evidence + Constraints → Execution Model |
| `build` | 在实际授权范围内实现并验证目标 | Authorized Intent / Plan → Reality + Verification |

Human 可以直接选择、跳过或重复任意 task。选择 task 不证明其他 task 已完成，也不会扩大 user prompt 的授权。详细职责、自治边界、Closure Check 和完成条件统一见 [Tasks README](.human-react/tasks/README.md)。

完整的多尺度循环和 Mermaid 图见 [实际循环](.human-react/loop.md)。Review、gap analysis 与 diagnosis 的概念边界见 [Review、Gap Analysis 与 Diagnosis](.human-react/tasks/review-and-diagnosis.md)。

## Core Boundaries

Human 控制宏观循环和外部效果授权；Agent 控制当前委托内的 micro ReAct。task 之间不自动转换，任何名称、前序结果、Memory 或 Lens 都不能代替当前授权。完整不变量见 [Workspace README](.human-react/README.md)，task 自治、Handback 与 Closure 规则见 [Tasks README](.human-react/tasks/README.md)，输出边界见 [Templates README](.human-react/templates/README.md)。

## Current Status

当前已经形成的是文档级设计：

- [`tasks/README.md`](.human-react/tasks/README.md) 定义 task 体系；
- [`loop.md`](.human-react/loop.md) 描述 Human 维持的实际循环；
- [`templates/README.md`](.human-react/templates/README.md) 定义 chat projection；
- [`memory/**`](.human-react/memory/) 与 [`lenses/**`](.human-react/lenses/) 描述尚未接入的 Project Context Layer。

`review` 已有第一版 task prompt 和 chat projection；`shape / plan / build` 的 task-specific prompt 与 template 仍为空。项目没有实际 Project Profile、Topic Memory、正式 Lens、自动加载、Memory 写入、adapter、schema、生成器或安装器。

## Directory

```text
.human-react/
├── README.md
├── loop.md
├── tasks/
│   ├── README.md
│   ├── review-and-diagnosis.md
│   ├── review.md
│   ├── shape.md
│   ├── plan.md
│   └── build.md
├── templates/
│   ├── README.md
│   ├── review.md
│   ├── shape.md
│   ├── plan.md
│   └── build.md
├── memory/
│   ├── README.md
│   └── topics/
│       └── README.md
└── lenses/
    ├── README.md
    ├── perspectives/
    │   └── README.md
    ├── postures/
    │   └── README.md
    └── project/
        └── README.md
```

## Documentation Ownership

| Document | Sole Responsibility |
| --- | --- |
| [`README.md`](README.md) | 项目定位、公开入口、当前状态和文档导航 |
| [`.human-react/README.md`](.human-react/README.md) | 可嵌入 Workspace 的入口、最小不变量和模块状态 |
| [`tasks/README.md`](.human-react/tasks/README.md) | task 共同规则、详细职责、边界和完成条件 |
| [`loop.md`](.human-react/loop.md) | Human 维持的宏观循环、同-task 循环和反馈路径 |
| [`review-and-diagnosis.md`](.human-react/tasks/review-and-diagnosis.md) | Review、gap analysis 与 diagnosis 的窄概念说明 |
| [`templates/README.md`](.human-react/templates/README.md) | Task Result 的公共骨架、条件投影和表达边界 |
| [`memory/README.md`](.human-react/memory/README.md) | 未接入的 Project Memory 候选模型 |
| [`lenses/README.md`](.human-react/lenses/README.md) | 未接入的 Lens 分类、联动和共同边界 |

详细文档只能定义其负责的语义；其他 README 应链接而不是复制。

## Relationship With Workflow Lite

[`sandbox/workflow`](sandbox/workflow/) 中的 Workflow Lite 只作为结构和设计思路参考。Human ReAct 借鉴显式 task、规范输出、Lens 和外部化上下文的价值，但不是兼容版本，也不继承其 task taxonomy、session lifecycle、artifact、persist、sync 或 archive 体系。

核心差异是：

> Human 维护目标、context、授权和反馈循环；task 表达本轮希望更新的状态，Agent 负责委托内部的 micro ReAct loop。
