# Human ReAct

Human ReAct 是面向现代 Agent harness 的轻量 Prompt Workspace。Human 维持宏观目标、context、价值取舍和现实授权，选择本轮需要的结果；Agent 在当前委托内自主观察、推理、使用工具、执行和验证，然后 Handback。

它不是线性 workflow、自动 task graph、Agent orchestration 平台或文档维护系统。

## Result-oriented Tasks

Task 按 Human 当前需要的结果选择，而不是按最终项目目标选择：

| Task | Human 当前需要 | State Transition |
| --- | --- | --- |
| `orient` | 理解背景、结构、机制或不同视角 | Subject + Learning Need + Available Context → Scoped Explanatory Model |
| `review` | 判断现状、gap、原因或适用性 | Existing Target → Evidence Context |
| `shape` | 对齐语义并构造尚未承诺的方向 | Human Expression + System Context + Agent Modeling Contribution → Decision Space |
| `plan` | 判断必要修改并形成执行方案 | Decision Space + Evidence + Delegation + Current State → Required Delta + Execution Model when needed |
| `build` | 确认或改变现实并验证 | Requested Outcome + Current Reality + Authorized Change → Verified Reality + Actual Change when required + Loop Closure Observation |

搜索、追踪、diagnosis、工具调用和验证是 task 内部能力。Human 可以直接选择、跳过或重复任意 task；系统不会自动转换 task。

## Core Model

```text
Human chooses a result and boundary
→ Agent runs a bounded micro ReAct
→ Task Result exposes outcome, evidence and material boundaries
→ Human interprets the result and chooses the next loop
```

核心约束：

- Human 是 macro loop 的 center agent；
- task 名称、前序结果、Memory、普通 Lens 或 Human 沉默不产生现实授权；Human 显式选择 effectful Lens 时只授权其 declared sidecar；
- Fact、Assumption、Conditional、Candidate、Decision、Planned Change、Authorized Change 和 Actual Change 不自动相互晋升；
- Agent 在委托内自主协调冲突，material reconciliation 对 Human 可见；
- 新目标、显著 scope expansion、新权限或重要风险选择需要 Handback；
- Build 结束当前 delivery loop，但只有 Human 能开启下一 loop。

完整共享协议见 [Human ReAct Core](.human-react/core.md)，task 选择和 prompt 见 [Tasks](.human-react/tasks/)，实际循环见 [Loop](.human-react/loop.md)，输出格式见 [Templates](.human-react/templates/)。

## Project Layers

| Layer | Purpose | Runtime |
| --- | --- | --- |
| [`.human-react/**`](.human-react/) | 可嵌入的 task、共享协议和 projection | 是 |
| [`design/**`](design/) | 已采纳设计的理由与 tradeoff | 否 |
| [`sandbox/**`](sandbox/) | 历史草案与外部参考 | 否 |

Design 不定义运行行为，sandbox 内容也不会自动晋升为当前设计或协议。

## Current Status

五个 task 均已有第一版 prompt 和 chat projection。当前系统是文档级协议，没有自动 task selection、runtime、adapter、generator 或 installer。

[`lenses/**`](.human-react/lenses/) 已提供 manual runtime composition v1：Human 为当前 task 显式选择 Lens，Agent 解析其直接依赖后形成 specialized micro ReAct。普通 Lens 不增加权限；Human 显式选择带 `effects` 的 Lens 时，只授权 metadata 声明的 protocol-owned sidecar。

[`memory/**`](.human-react/memory/) 已提供 episode-based Memory Capture v1：`memory-capture` Lens 为每次 Orient、Review 或 Build invocation 创建一个新文档，历史 capture 只由 Human 点名后手动加载。当前没有自动 loader、recall、index、resolver、deduplication、consolidation、schema validator、Tool provisioning 或 Skill。

## Directory

```text
human_react/
├── README.md
├── design/
│   ├── README.md
│   ├── lens-model.md
│   ├── memory-model.md
│   └── task-model.md
├── .human-react/
│   ├── README.md
│   ├── core.md
│   ├── loop.md
│   ├── tasks/
│   ├── templates/
│   ├── memory/
│   │   ├── README.md
│   │   └── captures/        # 首次真实 capture 时创建
│   └── lenses/
│       ├── README.md
│       ├── memory-capture.md
│       ├── poc.md
│       └── separated-analysis.md
└── sandbox/
    ├── todo/
    └── workflow/
```

## Documentation Ownership

| Owner | Responsibility |
| --- | --- |
| [`.human-react/core.md`](.human-react/core.md) | 跨 task 运行语义 |
| [`.human-react/tasks/`](.human-react/tasks/) | taxonomy、Prompt Contract 与 task-specific 行为 |
| [`.human-react/templates/`](.human-react/templates/) | 公共和 task-specific chat projection |
| [`.human-react/loop.md`](.human-react/loop.md) | task 之间的 Human-controlled loop |
| [`.human-react/lenses/`](.human-react/lenses/) | Lens contract、索引与正式 Lens |
| [`.human-react/memory/README.md`](.human-react/memory/README.md) | Memory load、episode capture 与 Human maintenance |
| [`design/task-model.md`](design/task-model.md) | 五 task 的非运行时设计理由 |
| [`design/lens-model.md`](design/lens-model.md) | Lens 分工、平铺结构与显式组合的设计理由 |
| [`design/memory-model.md`](design/memory-model.md) | 原子 capture、非增量维护与 macro loop 的设计理由 |

其他文档只提供摘要和链接，不建立平行定义。

## Relationship With Workflow Lite

[`sandbox/workflow`](sandbox/workflow/) 只提供结构和思路参考。Human ReAct 借鉴显式 task、规范输出、Lens 和外部化 context 的价值，但不兼容其 taxonomy、session lifecycle、artifact、persist、sync 或 archive 体系。

核心差异是：Human 维护目标、context、授权和反馈循环；task 表达本轮需要更新的状态，Agent 负责委托内部的 micro ReAct。
