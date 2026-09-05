# Human ReAct Lenses

Lens 是由 Human 为当前 task 手动选择的 runtime modifier。它补充关注角度、证据期待和项目坐标系，但不改变 task 的结果类型、公共 projection、scope 或 macro loop。普通 Lens 不产生现实权限；带有 `effects` 的 Lens 只有在 Human 显式选择后，才获得 metadata 所声明的窄 effect authorization。

```text
Task Contract
+ Human-selected Lens
+ resolved direct dependencies
→ specialized micro ReAct
```

没有显式选择时，Lens 不生效，五个 task 继续按自身 contract 运行。显式选择要求 Human 在当前请求中点名 Lens id 或文件，并表达将其用于当前 task 的意图；Profile recommendation、Memory、scope match、Agent inference、文件存在或工具可用都不能代替这项选择。

## File Contract

每个正式 Lens 是 `.human-react/lenses/<id>.md`：

```yaml
---
id: separated-analysis
type: perspective
activation: explicit-only
applies_to:
  - orient
  - review
  - shape
---
```

- `id` 必须全局唯一且使用 kebab-case；文件名必须是 `<id>.md`；
- `type` 只允许 `perspective`、`posture` 或 `project-trace`；类型是语义分类，不是目录层级；
- `activation` v1 只允许 `explicit-only`；
- `applies_to` 只列出 Lens 可以修饰的现有 task；不匹配时不应用该 Lens；
- `scope` 仅 `project-trace` 必填且必须为非空列表，其他类型省略；它描述适用范围，不触发 scope matching；
- `effects` 可选；存在时必须声明固定 effect、scope、时机和上限；
- 空的可选 metadata 整段省略。

`project-trace` 至少额外声明一个真实、受限的项目 scope：

```yaml
scope:
  - <bounded-project-scope>
```

正文统一包含 `Purpose / Use When / Attention / Evidence Expectations / Fallback / Stop Conditions / Boundaries`。Lens 不定义独立 Task Result、Status、路由或 task transition；declared effect 仍通过当前 task 的 projection 披露。

## Direct Dependencies

Lens 可以声明以下直接依赖：

```yaml
skills:
  required:
    - <skill-id>
  optional:
    - <skill-id>
tools:
  required:
    - <capability-id>
  preferred:
    - <capability-id>
references:
  - references/<lens-id>/<document>.md
```

- `skills.required` 在 Lens 被显式选择后必须解析并读取；缺失时遵循该 Lens 的 Fallback，降级或 Handback，不自动安装；
- `skills.optional` 只在当前 task 确有信息价值时解析和读取；
- `tools.required` 是完整应用 Lens 所需的 capability；缺失时遵循 Fallback。`tools.preferred` 只表达有信息价值的优先选择。两者是稀疏 capability 声明，不是允许工具的完整白名单；
- Lens 未指定时，Agent 保留当前 task 原有的工具自治，可以使用 available、Task-relevant、scope 内且已获授权的 capability。v1 不定义 Lens 工具黑名单；
- Tool available 不等于 authorized。普通调用仍受当前 task、user prompt、scope 和权限约束；显式选择 effectful Lens 只为其 metadata 中的 declared effect 提供窄授权；
- `references` 相对 `.human-react/lenses/` 解析并按需读取，必须留在当前项目允许读取的文档范围内；
- Lens 不得依赖或激活另一个 Lens，不建立递归组合；
- 声明依赖不会自行触发 resolver、installer、provisioning、文件读取或工具调用。

复杂资料未来放在 `lenses/references/<lens-id>/`；只有确有内容时才创建。当前 v1 没有自动 loader、resolver、installer、registry 或 schema validator，Human 与 Agent 在手动组合时解释 metadata。

## Declared Effects

Effectful Lens 使用：

```yaml
effects:
  create_documents:
    root: .human-react/memory/captures/
    timing: activation
    max_per_task: 1
    preexisting_documents: read-only
```

- Human 显式选择带 `effects` 的 Lens，是当前 prompt 对这些已声明 effect 的授权；Lens 文件存在、recommendation、Memory、scope match 或 Agent inference 都不能替代选择；
- `root` 相对项目根目录解析，并构成 effect 的最外层边界；不得回退到其他路径；
- `timing: activation` 表示 Lens 开始应用时立即创建文档；
- `max_per_task` 限制当前 task invocation 的创建数量；
- `preexisting_documents: read-only` 表示只可在 Handback 前填充本轮新建文档，不得修改选择 Lens 前已经存在的文档；
- 未声明的 effect 没有获得授权。Effect failure 遵循 Lens Fallback，不触发安装、provisioning、替代写入或其他权限扩张。

v1 只正式使用 `create_documents` effect，不建立通用 effect executor 或自动 enforcement。Hard permission、不可逆操作和声明之外的 effect 继续由 user prompt、task 与 harness 管理。

## Semantic Types

| Type | Responsibility |
| --- | --- |
| `perspective` | 提供跨项目可复用的分析角度、认识状态分离或证据标准 |
| `posture` | 把 Human 明确选择的交付姿态解释成当前 task 内的取舍约束 |
| `project-trace` | 提供真实项目特定的阶段模型、证据入口和调查坐标系 |

`project-trace` 固定的是诊断坐标系，不是结论。只有真实项目能够提供明确 `scope`、适用版本、sources of truth、pipeline、阶段 contracts、invariants、evidence entry points、first-divergence strategy、failure heuristics、verification 和 staleness check 时，才应创建正式 Lens。启发式必须与 evidence 分离，调查默认从 symptom 按需扩展，不完整回放所有阶段。

## Runtime Boundaries

- Lens 是 task modifier，不是 task、workflow stage、prompt alias 或自动路由规则；
- 当前 task 继续拥有 Responsibility、Working Policy、Handback、Status 和 Result Projection；
- Lens 可以提高证据要求或缩窄工作姿态，不能把 heuristic 晋升为 evidence；
- Lens 不保存 Profile、session state、调查结论或 Human decision；
- 普通 Lens、Skill、Tool 或 Memory 都不能扩大 Build authorization 或其他现实干预权限；effectful Lens 的显式 Human selection 也只授权 metadata 声明的 protocol-owned sidecar；
- Lens 与当前 prompt、项目 evidence 或 task contract 冲突时，当前 prompt、task contract 与现实证据共同约束结果，并披露会实质影响结果的 reconciliation；
- task 结果不会自动写回 Lens。

## Lens Index

| Lens | Type | Applies To | Purpose |
| --- | --- | --- | --- |
| [`separated-analysis`](separated-analysis.md) | `perspective` | Orient, Review, Shape | 分离观察、推断、条件延展与猜想 |
| [`poc`](poc.md) | `posture` | Review, Shape, Plan, Build | 以明确 Demo Contract 收敛 required-now delivery |
| [`memory-capture`](memory-capture.md) | `perspective` | Orient, Review, Build | 为当前 episode 创建并填充一个稳定 Memory capture |

运行规范只以本目录及其他 `.human-react/**` 文档为准；设计理由见 [`../../design/lens-model.md`](../../design/lens-model.md)。
