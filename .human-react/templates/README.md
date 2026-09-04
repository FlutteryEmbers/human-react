# Chat Projection Templates

本目录定义 Human ReAct 的 chat projection。Template 只把 task semantic result 组织成 Human-readable conversation checkpoint，不定义 task procedure、工具权限、持久状态或自动路由。

## Files

- [`common.md`](common.md)：所有 task 共享的 Task Result、状态、语义边界与 material disclosure；
- [`orient.md`](orient.md)：Scoped Explanatory Model；
- [`review.md`](review.md)：evidence-backed findings、gap、diagnosis 与 uncertainty；
- [`shape.md`](shape.md)：Current Take、Decision Space 与 Model Delta；
- [`plan.md`](plan.md)：Chosen Approach、Change Surface、Execution Model 与 Verification；
- [`build.md`](build.md)：Actual Changes、Verification、Loop Closure 与 Reusable Insight。

五个 task projection 均已有第一版。

## Composition

一个完整 task contract 由三部分组成：

```text
tasks/<task>.md
+ templates/common.md
+ templates/<task>.md
```

- Task prompt 定义 responsibility、allowed operations、boundaries、Handback 与 completion；
- Common projection 定义所有结果共享的输出协议；
- Task-specific projection 只定义当前 task 的 Context 结构和条件 section。

Task 的职责和权限边界优先。Template 只能组织表达，不能扩大 user prompt 的 scope、授权或外部效果。

## Design Boundary

- 当前 conversation 是 working context，不生成外部 handoff packet；
- projection 采用 progressive disclosure，空 section 直接省略；
- task-specific template 不重新定义公共字段或共享语义；
- README 只负责导航和文件职责，不承载运行协议；
- 没有 schema、generator、loader、自动 task selection 或持久化机制。

Task 体系见 [Tasks README](../tasks/README.md)，完整公共输出协议见 [Common Chat Projection](common.md)。
