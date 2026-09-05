# Human ReAct Design

本目录记录已经被主系统采纳的设计理由，帮助维护者理解 Human ReAct 为什么采用当前结构。它不是运行协议，不进入默认 harness prompt，也不定义 task 行为、输出格式或权限。

- 运行协议以 [`.human-react/**`](../.human-react/) 为准；
- [`task-model.md`](task-model.md) 解释五个 task 的设计理念与边界；
- [`lens-model.md`](lens-model.md) 解释 Lens 的角色、平铺 metadata 与显式能力组合；
- [`memory-model.md`](memory-model.md) 解释 episode capture、非增量维护与 Human-governed loading；
- [`sandbox/**`](../sandbox/) 是历史草案和未采纳探索，不会自动晋升为 design；
- design 与运行协议不一致时，应修订 design 以反映当前协议，而不是从 design 推导新的运行行为。

本目录不维护 ADR、版本历史、决策日志或自动同步机制。讨论历史只在仍能解释当前设计时才被提炼进来。
