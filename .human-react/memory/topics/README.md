# Topic Memory Reservation

本目录预留 bounded、project-specific Topic Memory，目前不包含实际记忆。

候选 Topic Memory 用于保存重新获取成本较高、且未来仍可能影响判断的 current understanding，例如 scoped Human decisions、复杂诊断结论、证据入口、项目约束、open questions 和有限证据支持的 heuristic。

一个候选 topic 应优先保持为当前视图，而不是追加式日志：

```text
Previous Memory
+ New Human Decision
+ New Evidence
- Invalidated Assumption
= Current Topic Memory
```

不保存完整 transcript、工具流水账、每轮 task 状态或普通 Build 修改列表。历史变化未来可以由 Git 保留，不设计 archive lifecycle。

现阶段不得创建实际 topic，也不存在自动 discovery、recall、write、compact 或 forget 行为。
