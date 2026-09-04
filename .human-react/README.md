# Human ReAct Workspace

本目录是可嵌入宿主项目的 Human ReAct Prompt Workspace。本文只负责 Workspace 导航、最小不变量和模块接入状态；task、loop、template 与 Project Context 的具体语义由各自目录文档负责。

## Core Model

Human 维持宏观目标、上下文和授权边界，并通过 user prompt 选择本轮希望得到的结果。Agent 在当前委托边界内自主运行 micro ReAct loop；完成或触及边界后返回 Human，由 Human 决定下一轮如何继续。

五个顶层 task 是 `orient / review / shape / plan / build`。它们是独立的结果委托，不是固定 workflow stage。统一入口和详细规则见 [Tasks README](tasks/README.md)，实际反馈循环见 [Loop](loop.md)。

## Minimum Invariants

1. Human 负责宏观目标、授权边界、结果解释和下一轮委托；
2. task 表示本轮结果类型，不表示固定流程位置；
3. Agent 的自治覆盖当前 task 所需的局部观察、推理、工具调用和验证；
4. task 名称、前序结果、Memory 或 Lens 本身不构成执行授权；
5. task 之间不存在自动转换；
6. Candidate、Human Decision、Resolved Choice、Planned Change、Authorized Change 与 Actual Change 不能相互自动晋升；
7. Human 显式发起 Plan 只委托当前边界内的 means-level decision closure，不构成 Build authorization；
8. Agent 默认在当前 authority 内自主协调 context 冲突，实质影响结果的 reconciliation 必须向 Human 披露；
9. 改变目标、显著扩大 scope、取得新权限或作出重要取舍时必须 Handback；
10. Task Closure 是内部核对，不生成公共必填 `Reflection` 字段；
11. Build 以 verified reality 和 Loop Closure Observation 结束当前 delivery loop，但不决定是否开启下一 loop；
12. task 完成后返回结果和边界，不自动开始下一 task。

## Module Map

| Module | Responsibility | Current Status |
| --- | --- | --- |
| [`tasks/**`](tasks/) | task 共同规则、详细定位与 task prompt | Orient / Review / Shape / Plan / Build v1 已设计 |
| [`loop.md`](loop.md) | Human 宏观循环、同-task 收敛和反馈路径 | 设计文档 |
| [`templates/**`](templates/) | Human-readable conversation checkpoint | Orient / Review / Shape / Plan / Build v1 已设计 |
| [`memory/**`](memory/) | Project Profile 与 Topic Memory 候选模型 | 未接入的 design reservation |
| [`lenses/**`](lenses/) | Perspective、Posture 和 Project Trace Lens 候选模型 | 未接入的 design reservation |

## Project Context Layer（未接入）

Memory 与 Lens 未来可能共同提供项目特化：

```text
Task            Human 本轮需要的结果类型
Project Profile 当前 posture、约束、偏好和 Lens bindings
Topic Memory    按 scope 召回的项目理解
Lens            将项目 context 转化为 task 内行为
```

当前没有实际 `profile.md`、Memory topic 或正式 Lens，也没有自动 recall、memory write、scope matching、binding resolution 或 prompt loading。`tasks/**` 与 `templates/**` 不消费这层内容，Memory/Lens 不参与 task 选择、Handback 或授权判断。

Project Context Layer 的候选模型分别见 [Memory](memory/README.md) 与 [Lenses](lenses/README.md)。只有在后续确定读写权限、优先级、激活、组合、加载和失效检查规则后，它才可能接入主系统。

## Documentation Rule

每个概念只在其 owner 文档中定义：

- task 语义归 [`tasks/README.md`](tasks/README.md)；
- 循环关系归 [`loop.md`](loop.md)；
- 公共输出协议归 [`templates/common.md`](templates/common.md)，目录导航归 [`templates/README.md`](templates/README.md)；
- task-specific 输出归对应的 `templates/<task>.md`；
- Memory 与 Lens 的候选设计归各自目录。

其他文档只提供摘要和链接，不建立平行协议。
