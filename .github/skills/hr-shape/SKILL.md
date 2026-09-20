---
name: hr-shape
description: 用户显式调用 /hr-shape 时，使用 Human ReAct shape task 构造和比较尚未承诺的候选方向。
user-invocable: true
disable-model-invocation: true
---

# Human ReAct Shape

本入口将用户的显式调用解释为选择 `shape`，命令后的文字是本轮请求。保留对象、上下文和具体操作限制；本轮及续轮遵循所选 Task，只有用户明确重新选择才切换，不自动运行下一 Task。Skill 不切换宿主运行模式，也不增加工具或操作权限。

## 读取运行协议

开始工作前，逐一显式读取以下文件；链接只是文件位置，不意味着内容或其依赖已经加载。相对路径以本 Skill 文件所在目录为基准。

1. [共享 Core](../../../.human-react/core.md)
2. [Shape Task](../../../.human-react/tasks/shape.md)
3. [公共输出模板](../../../.human-react/templates/common.md)
4. [Shape 输出模板](../../../.human-react/templates/shape.md)

按这些协议执行用户请求并交还结果；详细行为与输出以原文件为准。本入口不建立平行协议。必要协议文件缺失时说明缺失及其影响，不假装已读取或以通用提示替代。

## 显式上下文

仅在用户明确选择 Lens 时，读取 [Lens 协议](../../../.human-react/lenses/README.md)、所选 Lens 及其适用的直接依赖，按原规则组合。仅在用户点名 Memory capture 时，读取 [Memory 协议](../../../.human-react/memory/README.md) 和指定文件。不从工具可用性或请求相似度自动加载 Lens、回忆 Memory 或写入 capture。

## Copilot 可选交互

显式读取 [共享 Human decision 面板适配](../_shared/human-decision.md)。它为 Shape 中影响当前工作的重要 Human-owned 分歧提供交互，不要求每个 Candidate 都获接受；决策边界与结果以原协议为准。
