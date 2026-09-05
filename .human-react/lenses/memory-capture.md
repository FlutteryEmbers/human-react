---
id: memory-capture
type: perspective
activation: explicit-only
applies_to:
  - orient
  - review
  - build
tools:
  required:
    - project-filesystem-write
effects:
  create_documents:
    root: .human-react/memory/captures/
    timing: activation
    max_per_task: 1
    preexisting_documents: read-only
---

# Memory Capture

## Purpose

在当前 Orient、Review 或 Build episode 中捕获稳定、可复用且有明确 evidence boundary 的 Operational Friction 与 Project Bearing，同时保持原 task 的结果责任和 Human 对未来加载、保留与删除的控制。

## Use When

Human 在当前请求中显式选择本 Lens 时使用。该选择同时表达创建独立 Memory capture 文档的意图，并构成对 metadata 所声明 `create_documents` effect 的授权；没有这项显式选择时不得创建 Memory。

## Attention

- 正常完成当前 task，不把 Memory 捕获变成主要结果；
- 只关注实际发生、已经验证且在相同环境中可能复发的工具或环境问题；
- 只关注来源明确、跨 task 可复用且重新发现成本较高的稳定项目坐标；
- 同一 invocation 的所有合格条目写入 activation 时创建的唯一文档，不查看历史 capture 寻找重复或统一表述。

## Evidence Expectations

- Operational Friction 同时具备真实 Trigger、Observed Failure、已执行 Resolution、成功 Verification、Applies When 和 Invalid When；
- Project Bearing 具备 Statement、`observed | human-confirmed` Basis、Source、Scope、Invalid When 和 Boundary；
- Review 不保存未验证的修复方向，Build 不把普通实现变化或单次成功测试自动概括为稳定经验；
- credential、敏感输出、权限绕过方法和超出当前 evidence 的 claim 不进入 capture。

## Fallback

缺少 `project-filesystem-write` capability、effect scope 不可用或写入被拒绝时，不安装能力、不改写其他路径，也不阻止 Agent 形成主 task 的有用结果。Task Result 使用 `partial`，并在 `Context` 中披露 `Memory Capture` failure 和原因；只有主 task 本身也无法形成任何有用结果时才 `blocked`。

## Stop Conditions

- 当前 task 已完成，所有分别通过 Capture Gate 的内容已写入本轮唯一文档；
- 继续捕获只会保存普通工具过程、临时状态、未验证推断或一般知识；
- 继续写入需要第二个文档、修改历史 capture、越出声明目录或取得其他权限。

## Boundaries

本 Lens 只授权在 `.human-react/memory/captures/` 创建并在当前 invocation 内填充一个新文档。它不修改历史 Memory，不生成 corpus Summary、index 或 consolidation，不激活其他 Lens，不改变当前 Task Result schema，也不授权修改 Orient/Review target 或 Build request 之外的业务现实。
