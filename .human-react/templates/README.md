# Chat Projection Templates

本目录把 task semantic result 组织为 Human-readable conversation checkpoint。Template 不定义 task procedure、权限、持久状态或自动路由；Human 显式选择 effectful Lens 后，只在现有 `Context` 中投影 declared sidecar receipt。

## Files

- [`common.md`](common.md)：公共头部、Status、Request Interpretation、共享标签、Reconciliation 和压缩规则；
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

Task prompt 定义所选 task 内的行为，Common 定义共享输出和实质改写披露，task-specific template 组织主要结果与必要辅助内容。Template 不自行重选 task，也不扩大具体操作授权。

## Design Boundary

- 使用 progressive disclosure；没有实质改写或其他补充信息时可省略 Context，发生实质改写时必须保留 Request Interpretation；
- 一份所选 Task Result 按解释后的请求判断完成度；未执行的原文动作在改写边界说明，不自动变成待办或降低状态；
- 每项 material information 只有一个主要归属，不跨 section 重复展开；
- 默认示例表示普通最小结果，不是完整字段清单；
- 当前状态已满足目标时，不生成空 Change Surface、Execution Model、Actual Changes 或 no-op packet；
- internal Gate、Reflection、工具流水账和自动 next-task 不进入 projection；
- 没有 schema、generator、loader、自动持久化或 external handoff；declared Lens effect 由对应 Lens contract 定义，不由 template 创建。

共享运行语义见 [Core](../core.md)，task 体系见 [Tasks](../tasks/README.md)。
