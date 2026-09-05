---
id: separated-analysis
type: perspective
activation: explicit-only
applies_to:
  - orient
  - review
  - shape
---

# Separated Analysis

## Purpose

将不同认识状态和解读角度分开，避免可观察内容、证据推断、条件推演与创造性猜想互相污染。

## Use When

Human 在 Orient、Review 或 Shape 中显式选择本 Lens，且 target 包含容易混淆的事实陈述、解释、推断、叙事信号或假设延展时使用。它适合需要清楚说明“看到了什么、由此能推出什么、仍不知道什么”的任务。

## Attention

按 target 需要选择相关维度，不要求每次全部展开：

| Dimension | Attention |
| --- | --- |
| Observable Content | 只描述可观察的言语、行为、claim、代码或文本结构，不赋予未被证据支持的动机 |
| Emotional Signals | 只在叙事、人际互动或对话存在足够语言信号时，根据措辞、节奏或重复主题提出明确标注的推断 |
| Evidence Logic | 区分 premise、claim、evidence、inference、alternative explanation 和 unknown |
| Conditional Extension | 先声明 premise，再补全逻辑链；不把条件当成现实事实 |
| Hypotheses | 作为可验证的探索方向，不作为 evidence 或已确认结论 |

对一般文章或技术对象，优先分析 claims、evidence、argument structure、assumptions、contradictions 和 omissions。没有足够信号时省略 Emotional Signals，不为填满结构而猜测。

## Evidence Expectations

- 每个重要结论都能追溯到 observable evidence、明确 premise 或已标注的 inference；
- alternative explanation 与 unknown 在会改变判断时保持可见；
- Conditional Extension 不更新现实状态、Human decision 或权限；
- Review 的 finding 继续遵守 Review 自身的证据纪律，Shape 的 Candidate 继续保持未承诺状态。

## Fallback

某个维度缺少足够 evidence 时省略该维度或标记为 unknown。Lens 与 task contract 或现实 evidence 冲突时，以 task contract 和 evidence 为约束，在当前 task 内披露 material reconciliation；若缺失信息使当前结果完全无法成立，则按当前 task Handback。

## Stop Conditions

- 已经分清支持当前结果所需的观察、推断、条件和未知；
- 继续分析只会产生无证据的心理归因、动机猜测或与请求无关的解释分支；
- 需要新的目标、scope、权限或 Human value decision 才能继续。

## Boundaries

本 Lens 不定义固定分段输出、全局回答风格、独立 template、Status 或 task transition。它不把 Orient 变成 Review，不替 Shape 作出 Human decision，也不提供 Build authorization。
