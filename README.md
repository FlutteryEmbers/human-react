# Human ReAct

Human ReAct 是面向现代 Agent harness 的轻量 Prompt Workspace。Human 维持宏观目标、context、价值取舍和现实授权，选择本轮需要的结果；Agent 在当前委托内自主观察、推理、使用工具、执行和验证，然后 Handback。

它不是线性 workflow、自动 task graph、Agent orchestration 平台或文档维护系统。

## Result-oriented Tasks

Human 选择 Task 表达本轮协作意图。所选 Task 对 prompt 有最高意图解释优先级，决定主要结果责任，而非固定项目阶段：

| Task | Human 当前需要 | State Transition |
| --- | --- | --- |
| `orient` | 理解背景、结构、机制或不同视角 | Subject + Learning Need + Available Context → Scoped Explanatory Model |
| `review` | 判断现状、gap、原因或适用性 | Existing Target → Evidence Context |
| `shape` | 对齐语义并构造尚未承诺的方向 | Human Expression + System Context + Agent Modeling Contribution → Decision Space |
| `plan` | 判断必要修改并形成执行方案 | Decision Space + Evidence + Delegation + Current State → Required Delta + Execution Model when needed |
| `build` | 确认或改变现实并验证 | Requested Outcome + Current Reality + Authorized Change → Verified Reality + Actual Change when required + Loop Closure Observation |

Agent 在所选 Task 内解释 prompt，保留对象、关注目标和具体约束，并完成该 Task 明确允许且直接支持主要结果的辅助分析。措辞冲突通过 Task-scoped Request 消解，实质改写在结果中披露；不因此要求确认、切换 Task 或降低完成状态。只有 Human 明确重新选择 Task 才改变本轮及续轮选择。

## Core Model

```text
Human selects a Task and supplies prompt context
→ Agent forms a Task-scoped Request
→ Agent runs a bounded micro ReAct
→ Task Result exposes outcome, evidence and material Request Interpretation
→ Human interprets the result and chooses the next loop
```

核心约束：

- Human 是 macro loop 的 center agent；所选 Task 决定本轮意图解释，prompt 改写不创造事实、scope 或操作权限；具体现实操作只能来自 Human 明确请求或其无歧义引用且仍有效的既有委托；
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

五个 task 已提供以所选 Task 为意图锚点的 prompt 和 chat projection，实质改写通过 Request Interpretation 披露。当前系统是文档级协议，已提供 Copilot Skills v1 手动入口；没有自动 task selection、执行 runtime、generator 或 installer。

[`lenses/**`](.human-react/lenses/) 已提供 manual runtime composition v1：Human 为当前 task 显式选择 Lens，Agent 解析其直接依赖后形成 specialized micro ReAct。普通 Lens 不增加权限；Human 显式选择带 `effects` 的 Lens 时，只授权 metadata 声明的 protocol-owned sidecar。

[`memory/**`](.human-react/memory/) 已提供 episode-based Memory Capture v1：`memory-capture` Lens 为每次 Orient、Review 或 Build invocation 创建一个新文档，历史 capture 只由 Human 点名后手动加载。当前没有自动 loader、recall、index、resolver、deduplication、consolidation、schema validator、Tool provisioning 或 Skill。

## Copilot Skills v1

在 VS Code Copilot 中显式调用以下 Skill。入口按需读取原协议，不自动选择或串联 Task。

| 指令 | 示例 |
| --- | --- |
| `/hr-orient` | `/hr-orient 解释这个项目的 Task 与 Lens 如何组合` |
| `/hr-review` | `/hr-review 评价当前 Memory 设计的适用边界` |
| `/hr-shape` | `/hr-shape 比较两种面向团队的接入方向` |
| `/hr-plan` | `/hr-plan 为查询接口规划分页支持，保持旧客户端兼容` |
| `/hr-build` | `/hr-build 只检查 README 的本地链接是否有效，不修改文件` |

将本仓库的 `.github/skills/` 和 `.human-react/` 一起复制到目标仓库根目录，并保留相对位置。打开该仓库后，在 Copilot 聊天的 `/` 菜单检查五个 `hr-*` 入口；确保当前 Agent 能读取工作区文件。入口不会切换宿主模式或授予工具权限。只有 Skills 而缺少协议文件，不能构成完整接入。

`hr-orient` 提供可选的学习焦点面板：问题明确时直接解释；焦点不清且会影响解释时，先提供最小模型或例子，再帮助用户辨认需要的解释方向。选项说明将获得什么解释，用户无需先诊断自己哪里不懂。工具不可用或用户跳过时继续 Orient 原生流程，不强制文本问答。校准服务于理解，主要交付仍是解释模型。

`hr-plan` 提供可选的 Human conflict 面板：需要 Human 决策且当前会话允许使用 `vscode/askQuestions` 时优先提问，说明背景、冲突与每个选项的影响。工具缺失、失败或用户跳过时，回到原 Plan 的协调、Handback 和结果输出，不增加强制文本问答。面板不是必要依赖，选择方案也不授权 Build。

本版没有 Codex 入口或自动安装；平台交互说明留在 Copilot Skill 内，共享协议保持平台无关。参见 [Copilot 手工验收](tests/copilot-skills.md)。Skills 的发现、交互和续轮行为需要在实际 Copilot 环境验证，静态检查不代表宿主行为已通过。

## Directory

```text
human_react/
├── .github/skills/       # Copilot 手动 Task 入口
├── tests/               # Copilot 手工验收场景
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

核心差异是：Human 维护目标、context、授权和反馈循环；task 表达本轮主要结果责任，Agent 在其内部解释请求并完成 micro ReAct。
