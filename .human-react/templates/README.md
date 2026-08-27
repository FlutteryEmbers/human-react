# Chat Projection Templates

本目录存放 Human ReAct task 的默认 chat projection template：

- [`review`](review.md)
- [`shape`](shape.md)
- [`plan`](plan.md)
- [`build`](build.md)

Template 只定义 task 结果如何投影到 chat。它不保存实际输出，不维护 session 或 source-of-truth，也不扩大 task 或 user prompt 的授权边界。

四个 template 文件当前故意保持为空，只锁定目录与命名。具体格式将在 task prompt 设计时再补充。
