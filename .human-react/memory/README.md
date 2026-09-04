# Project Memory Design Reservation

本目录记录 Project Context Layer 中 Memory 的 high-level 候选设计。它目前不是主系统的一部分，不保存实际项目记忆，也不定义运行时读写行为。

## Candidate Model

Memory 不复刻 Workflow Lite 的 session lifecycle。它不使用 inbox、threads、archive、artifact type、persist 或 sync，而是提供两层项目上下文：

| Layer | Candidate Responsibility | Loading Model |
| --- | --- | --- |
| Project Profile | Human 确认的当前 posture、project-wide/scoped constraints、preferences、Demo Contract、upgrade triggers 和 Lens bindings | 未来可作为短小常驻 context |
| Topic Memory | 某个 bounded scope 的事实、决定、调查结论、heuristics 和 open questions | 未来按 target 定向 recall |

候选目录形态：

```text
memory/
├── README.md
├── profile.md           # 未来由 Human 显式创建；当前不存在
└── topics/
    └── <topic>.md       # 未来按需创建；当前不存在
```

## Normative And Descriptive Memory

Project Profile 主要保存 Normative Memory：

- `[Decision]`：Human 明确确认的当前决定；
- `[Constraint]`：适用 scope 内必须遵守的边界；
- `[Preference]`：user prompt 未另行说明时采用的默认倾向；
- current posture、Demo Contract 与 Lens bindings。

Topic Memory 主要保存 Descriptive Memory：

- `[Fact]`、`[Evidence]`、`[Assumption]`、`[Open]`；
- `[Invariant]`、`[Lesson]` 与 `[Heuristic]`，并保留相应证据强度；
- 复杂调查得到但仍可能变化的 current understanding。

Orient Result 是当前 conversation 中的 Scoped Explanatory Model，不会因为解释具有复用价值就自动成为 Project Memory。将其中的背景、模型或解释晋升为持久 context 需要未来单独设计的 Human-governed promotion；当前系统不提供该能力。

Normative Memory 可以约束未来 task 的默认做法，但不能单独授权 Build、扩大 scope 或允许新的外部效果。Descriptive Memory 是 context cache，不是 source of truth；高影响事实仍需回到当前代码、测试、规范或 Human decision 验证。

## Lens Binding Concept

未来的 `profile.md` 可以由 Human 显式声明 scoped binding，例如：

```text
project-wide          -> Posture Lens: poc
parser/call-chain     -> Project Trace Lens: kotlin-call-chain
```

候选组合关系为：

```text
Task
+ Project Profile
+ Relevant Topic Memory
+ Bound Lens
= Project-specialized Task Behavior
```

只有 Project Profile 中明确的 Human decision 才可能激活持续 Lens。普通 Topic Memory、Reusable Insight 或 Agent inference 不能创建 binding，也不能自动把经验晋升为项目规则。

## Candidate Priority

未来接入时建议遵循：

```text
Current Human Prompt / Explicit Authorization
> Scoped Human Decision and Constraint in Project Profile
> Active Posture Lens
> Scoped Project Trace Lens
> Topic Memory Fact / Heuristic
> Agent Default
```

当前 prompt 可以覆盖本轮默认值，但不会自动修改 Project Profile。发生实质冲突时，当前 task 先在自身 authority 内进行 Bounded Reconciliation，采用或保留适合本轮的 working basis，并向 Human 披露会影响结果的协调。只有需要将单轮 exception 晋升为持久 Project Profile、Lens binding 或其他 normative policy 时，才需要 Human 作出长期 context decision。

Observed evidence 约束 descriptive conclusion，但不会自动覆盖 Project Profile 中 Human 已确认的 Decision 或 Constraint。两者不一致时，task 应区分 actual state 与 normative state，而不是静默改写其中一方。

## Non-integration Status

当前必须保持：

- 不创建实际 `profile.md` 或 Topic Memory；
- 不从 task 自动 recall 或写入 Memory；
- Closure Check 与 Reusable Insight 不自动进入 Memory；
- Memory 不激活 Lens、不改变 template、不参与授权判断；
- 没有 memory task、loader、binding resolver、schema、index 或 archive。

只有在真实项目中先验证最小的 Project Profile + Posture Lens 组合后，才能决定是否接入主系统。
