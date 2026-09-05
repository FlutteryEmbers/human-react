# Human ReAct Memory

Memory 是由 Human 手动选择进入当前 task 的 project-specific additional context。它保存有来源和边界的稳定项目经验，不是 source of truth、task router、Lens selector、现实授权或 Agent 的隐藏长期状态。

Memory v1 使用 episode-based capture：Human 显式选择 [`memory-capture`](../lenses/memory-capture.md) Lens 时，每次 task invocation 创建一个新文档；文档可以包含多条合格记录，但不读取、合并、更新或总结历史 Memory。

## Manual Loading

Human 必须在当前请求中点名一个或多个 capture 文件，并表达将其用于当前 task 的意图，Memory 才进入 context。目录存在、文件相关、scope match、Agent inference 或前序 task 使用过该文件，都不能代替手动加载。

加载后的 Memory：

- 只提供 working context，不自动成为当前 Fact、Decision 或 Constraint；
- 仍需检查 entry 的 Source、Scope、Basis、Boundary 与 Invalid When；
- 不激活 Lens、不扩大 task scope、不授权 Build 或其他现实 effect；
- 默认只读，不因当前 evidence、task result 或 Agent reconciliation 被回写。

## Capture Document Contract

显式选择 `memory-capture` 后，在开始当前 task 时立即创建：

```text
.human-react/memory/captures/<YYYYMMDD-HHMMSS>-<task>.md
```

时间使用当前环境的本地时间；同秒发生文件名冲突时依次增加 `-2`、`-3`。目录不存在时随首个文档创建。一个 invocation 只创建一个文档，之后所有合格内容进入该文档；此前存在的 capture 文档不得修改。

新文档使用：

```yaml
---
id: 20260905-143210-review
kind: memory-capture
lens: memory-capture
task: review
created_at: 2026-09-05T14:32:10+08:00
---

# Memory Capture
```

- `id` 必须等于文件名 stem，发生冲突时使用相同数字后缀；
- `kind` 固定为 `memory-capture`，`lens` 固定为 `memory-capture`；
- `task` 使用当前 `orient | review | build`；
- `created_at` 使用带 UTC offset 的 ISO 8601 时间。

即使本轮没有合格记录，也保留只有 front matter 和标题的文档，不自动删除或添加总结。成功创建后，当前 Task Result 在 `Context` 中披露 `Memory Capture: <path>`。

## Eligible Entries

一个 capture 文档可以包含任意数量、分别通过 Capture Gate 的条目。没有 document-level Summary，也不为了填满结构而创建内容。

### Operational Friction

只记录 Agent 与真实工具或环境交互时发生的问题及已验证解决方案：

```markdown
## Operational Friction — <title>

- Trigger: <触发条件>
- Environment: <相关环境>
- Observed Failure: <实际失败>
- Resolution: <已成功执行的解决方法>
- Verification: <成功 evidence>
- Applies When: <复用条件>
- Invalid When: <失效条件>
```

必须同时满足：失败真实发生并对 task 有实际影响；Resolution 已成功执行；Verification 支持成功 claim；相同环境中可能复发；适用边界明确。Review 只有实际验证 Resolution 后才能记录，未解决 diagnosis 不进入 Memory。

### Project Bearing

只记录项目特定、来源明确、跨 task 有复用价值且重新发现成本较高的稳定坐标：

```markdown
## Project Bearing — <title>

- Statement: <稳定项目背景>
- Basis: observed | human-confirmed
- Source: <项目 evidence 或 Human confirmation>
- Scope: <适用范围>
- Invalid When: <需要重新检查的条件>
- Boundary: <不能据此建立什么>
```

合格内容包括 source of truth、真实入口、项目术语、稳定模块责任、pipeline boundary 或运行约定。临时实现状态、一般知识和没有来源的解释不进入 Memory。

## Capture Boundaries

- 不捕获未验证 workaround、普通工具流水账、Shape Candidate、Planned Change、Agent 推断的 Human preference、credential、敏感输出或权限绕过方法；
- 不把当前任务的新代码或单次测试自行晋升为普遍 Invariant；
- 不读取历史 capture 进行自动 recall、去重、冲突消解、合并、压缩或 consolidation；
- 不修改已有 capture；同一 episode 内只填充本轮新建的文档；
- capture document 是 protocol-owned sidecar，不改变当前 task 的结果类型，也不修改被 Orient 或 Review 处理的 target；
- Lens selection 只授权其 metadata 声明的目录、document 数量和生命周期，不提供其他持久写入权限。

## Human Maintenance

Human 以整个 episode 文档为主要治理单位，可以删除不希望保留的 capture，也可以显式要求局部编辑。系统不自动拆分、覆盖、更新或删除。

保留文档不表示 Human 已阅读、同意或确认其中内容；手动加载也只表示允许它进入当前 context。新 evidence 与旧 Memory 冲突时，当前 task 按 Bounded Reconciliation 形成 working basis，但不回写旧文档。

## V1 Boundary

当前没有 Profile、Topic Memory、自动 loader、recall、index、resolver、deduplication、consolidation、schema validator 或 archive lifecycle。Agent 依据文档 contract 手动解释 Lens metadata；首个真实 capture 才创建 `memory/captures/`，本仓库不提供示例或空目录。
