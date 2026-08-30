# Project Trace Lens Reservation

本目录预留 project-specific 的 Project Trace Lens，目前不包含正式 Lens。

Project Trace Lens 面向拥有稳定阶段模型、且诊断需要拼接多层证据的项目子系统。它比通用 Perspective Lens 更接近一个只读、无权限的 project skill：提供系统地图、证据协议和调查方法，但不提供 task routing、写入授权或独立执行入口。

未来它可以由 Project Profile 按明确 scope 绑定，并读取相关 Topic Memory 作为当前项目参数。Lens 自身不保存调查结论、当前偏好或 Human decision；Memory 内容也不能修改 Lens 的阶段模型或自动扩大其适用 scope。

## Candidate Package

当单文件无法保持短小，可以采用按需加载的 package：

```text
project/<lens-name>/
├── README.md
├── pipeline.md
├── evidence.md
└── failure-signatures.md
```

- `README.md`：适用 scope、版本、短阶段图和按需加载导航；
- `pipeline.md`：阶段、ownership、输入输出契约和 invariants；
- `evidence.md`：源码入口、probe、fixture、测试和证据要求；
- `failure-signatures.md`：symptom 到候选阶段的启发式映射。

只有确有 token 或关注点分离需求时才拆包；第一版候选应优先使用单文件。

## Required Semantics

一个候选 Project Trace Lens 应能表达：

- `Scope` 与不适用范围；
- `Version / Applicability`；
- `Sources of Truth`；
- `Pipeline` 与阶段 ownership；
- `Input / Output Contracts`；
- `Invariants`；
- `Evidence Entry Points`；
- `First-divergence Strategy`；
- `Known Failure Signatures`，明确其只是 heuristic；
- `Verification`；
- `Staleness Check`；
- `Boundaries`。

## Kotlin Call-chain Example

候选 Kotlin call-chain Lens 可以描述类似以下调查坐标系：

```text
Source
→ Tokenization
→ Syntax / PSI
→ Symbol Resolution
→ Candidate Collection
→ Overload Resolution
→ Type Inference
→ Call Normalization
→ Final Call Chain
→ Downstream Consumer
```

实际阶段必须来自项目当前实现，不能把通用 Kotlin 规范直接当成项目支持范围。诊断时应先确定 expected 与 actual，再定位 first divergence；只有证据需要时才向相邻和上游阶段展开。

## Non-integration Status

本目录只定义候选结构。不得据此自动创建 `kotlin-call-chain` 或任何其他正式 Lens，也不得触发自动 scope matching、文件加载、工具调用或 task 行为。
