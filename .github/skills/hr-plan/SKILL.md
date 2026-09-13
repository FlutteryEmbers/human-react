---
name: hr-plan
description: 用户显式调用 /hr-plan 时，使用 Human ReAct plan task 判断必要修改并形成执行方案。
user-invocable: true
disable-model-invocation: true
---

# Human ReAct Plan

本入口将用户的显式调用解释为选择 `plan`，命令后的文字是本轮请求。保留对象、上下文和具体操作限制；本轮及续轮遵循所选 Task，只有用户明确重新选择才切换，不自动运行下一 Task。Skill 不切换宿主运行模式，也不增加工具或操作权限。

## 读取运行协议

开始工作前，逐一显式读取以下文件；链接只是文件位置，不意味着内容或其依赖已经加载。相对路径以本 Skill 文件所在目录为基准。

1. [共享 Core](../../../.human-react/core.md)
2. [Plan Task](../../../.human-react/tasks/plan.md)
3. [公共输出模板](../../../.human-react/templates/common.md)
4. [Plan 输出模板](../../../.human-react/templates/plan.md)

按这些协议执行用户请求并交还结果；详细行为与输出以原文件为准。本入口不建立平行协议。必要协议文件缺失时说明缺失及其影响，不假装已读取或以通用提示替代。

## 显式上下文

仅在用户明确选择 Lens 时，读取 [Lens 协议](../../../.human-react/lenses/README.md)、所选 Lens 及其适用的直接依赖，按原规则组合。仅在用户点名 Memory capture 时，读取 [Memory 协议](../../../.human-react/memory/README.md) 和指定文件。不从工具可用性或请求相似度自动加载 Lens、回忆 Memory 或写入 capture。

## Copilot 可选交互

读取 [Human conflict 面板适配](references/human-conflict.md)。它只在原 Plan 已需要 Human 决策且提问工具可用时增强交互；没有工具时原 Plan 流程保持不变。
