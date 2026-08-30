# Perspective Lens Reservation

本目录预留跨项目复用的 Perspective Lens，目前不包含正式 Lens。

Perspective Lens 用于 Human 在单轮 task 中显式要求额外分析角度或证据强度，例如 architecture、boundary、debug、test 或 redteam。它不是持续的项目默认值，也不应包含项目专属流水线、项目事实或固定执行流程。

候选 Perspective Lens 至少需要说明：

- `Use When`：什么问题值得采用该角度；
- `Attention`：Agent 应额外关注什么；
- `Checks`：需要哪些检查或证据；
- `Stop Conditions`：何时不应继续扩大分析；
- `Boundaries`：不会改变哪些 task 职责和权限。

## Separated Analysis Candidate

`Separated Analysis` 是一个候选 Perspective Lens，用于将不同认识状态和解读角度分开，避免观察、推断、条件推演和创造性猜想互相污染。它可以按 target 需要选择以下维度，不要求每次全部启用：

| Dimension | Candidate Semantics |
| --- | --- |
| Observable Content | 只描述 target 中可观察的言语、行为、claim 或文本结构，不赋予动机 |
| Emotional Signals | 只在 target 存在语言或人际信号时，根据措辞、节奏或重复主题给出明确标注的推断，不写成事实 |
| Evidence Logic | 拆解 premise、claim、evidence、inference、alternative explanation 和 unknown |
| Conditional Extension | 声明显式 premise 后延展逻辑链，可补全中间步骤，但不创造事实或覆盖证据结论 |
| Hypotheses | 只作为可选探索方向，明确标记为非证据、非已确认逻辑结论 |

对一般文章，Observable Content 应优先解析 claims、evidence、argument structure、assumptions、contradictions 和 omissions。只有当 target 是叙事、人际互动或对话时，才考虑行为与 Emotional Signals；没有足够语言信号时省略情绪维度，不为填满结构而推测。

该候选 Lens 不定义固定五段输出，不附带全局回答风格、触发口令或独立 template。它只提供适用于当前 target 的分析维度，结果仍由当前 task template 投影。

现阶段不得在这里创建正式 Lens，也不得被 `tasks/**`、Memory 或 `templates/**` 引用。
