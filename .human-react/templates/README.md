# Chat Projection Templates

本目录把 task semantic result 组织为 Human-readable conversation checkpoint。Template 不定义 task procedure、权限、持久状态或自动路由；Human 显式选择 effectful Lens 后，只在现有 `Context` 中投影 declared sidecar receipt。

## Files

- [`common.md`](common.md)：公共头部、Status、共享标签、Reconciliation 和压缩规则；
- [`orient.md`](orient.md)：Scoped Explanatory Model；
- [`review.md`](review.md)：Finding、Evidence、Gap 与 Diagnosis；
- [`shape.md`](shape.md)：Decision Space 或 Model Delta；
- [`plan.md`](plan.md)：Required Delta、Change Surface 与 Verification；
- [`build.md`](build.md)：Actual Changes、Verification 与 Loop Closure。

## Composition

```text
tasks/<task>.md
+ templates/common.md
+ templates/<task>.md
→ Complete Task Contract
```

Task prompt 定义行为，Common 定义共享输出，task-specific template 只定义当前 Context。Task 权限和职责优先；template 不能扩大 user prompt。

## Design Boundary

- 使用 progressive disclosure；Context 和空 section 可整体省略；
- 每项 material information 只有一个主要归属，不跨 section 重复展开；
- 默认示例表示普通最小结果，不是完整字段清单；
- 当前状态已满足目标时，不生成空 Change Surface、Execution Model、Actual Changes 或 no-op packet；
- internal Gate、Reflection、工具流水账和自动 next-task 不进入 projection；
- 没有 schema、generator、loader、自动持久化或 external handoff；declared Lens effect 由对应 Lens contract 定义，不由 template 创建。

共享运行语义见 [Core](../core.md)，task 体系见 [Tasks](../tasks/README.md)。
