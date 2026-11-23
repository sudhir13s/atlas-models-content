---
id: fine-tuning-method/orpo
type: fine-tuning-method
slug: orpo
schema_version: 3.1
title: ORPO (Odds Ratio Preference Optimization)
method_type: preference
memory_profile: medium
summary: 'Fine-tune and align in one stage: the usual next-token loss on the preferred answer plus a small penalty, built from odds, against the rejected answer, with no reference model.'
when_to_use: When you start from a pre-trained model with preference pairs and want supervised fine-tuning and preference alignment in one stage, without a reference model.
related_hyperparameters:
- hyperparameter/learning-rate
profile:
  trains: The model being aligned, starting from the pre-trained model
  models_in_training: 'One: the model being trained'
  data: Prompts, each with a preferred and a rejected answer
  stages: One; no separate supervised fine-tuning
  source: https://arxiv.org/abs/2403.07691
typical_values:
- setting: 'ORPO paper: Mistral-ORPO-α and Mistral-ORPO-β (7B), UltraFeedback'
  value: λ = 0.1
  source: https://arxiv.org/abs/2403.07691
- setting: 'ORPO paper: Llama-2 (7B), UltraFeedback'
  value: λ = 0.2
  source: https://arxiv.org/abs/2403.07691
- setting: 'ORPO paper: Phi-2 (2.7B), UltraFeedback'
  value: λ = 0.25
  source: https://arxiv.org/abs/2403.07691
- setting: 'ORPO paper: maximum learning rate (AdamW or paged AdamW, linear warmup, cosine decay)'
  value: 8e-6
  source: https://arxiv.org/abs/2403.07691
- setting: 'ORPO paper: λ values in its ablation (Mistral 7B, UltraFeedback)'
  value: 0.1, 0.5, 1.0
  source: https://arxiv.org/abs/2403.07691
- setting: 'ORPO paper: length every input is truncated and padded to (UltraFeedback / HH-RLHF)'
  value: 2,048 / 1,024 tokens
  source: https://arxiv.org/abs/2403.07691
mistakes:
- mistake: Raising λ without checking tasks that have one right answer
  why: 'In the paper''s ablation on Mistral 7B, λ = 1.0 scored **worse** than λ = 0.1 on MT-Bench''s extraction, math and reasoning, and **better** on STEM, humanities and roleplay. The authors read this as over-adapting to the chosen answers, and write that a smaller λ is not always better: it depends on the need and the model.'
  fix: Compare two values of λ on the categories of your own evaluation.
- mistake: Reading the odds as the probability of the whole answer
  why: ORPO's P(y|x) is the **average** log-likelihood per token, exponentiated (equation 3), and the odds are P ÷ (1 − P) (equation 4). An answer whose tokens average a log-probability of −0.5 has P ≈ 0.61 and odds of about 1.54, whatever its length.
  fix: Compute the odds from the per-token average, as in the worked example.
- mistake: Adding a separate supervised stage and a reference model out of habit
  why: 'ORPO is built to need **neither** an SFT warm-up stage nor a reference model: its own next-token loss does the supervised part. The authors count half the forward passes per batch of DPO and RLHF, which run a reference model beside the trained one.'
  fix: Train from the pre-trained model on the preference pairs directly.
visual:
  kind: orpo_loss
  lambda: 0.1
  average_log_probabilities:
    chosen: -0.5
    rejected: -1.0
  caption: Each answer's average token probability and odds, and the two parts of the ORPO loss, computed from the average token log-probabilities of the worked example with λ = 0.1, the value used for Mistral-ORPO. The log-probabilities are an example, not from a trained model.
tags:
- fine-tuning
- preference
- orpo
- alignment
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/2403.07691
  title: 'ORPO: Monolithic Preference Optimization without Reference Model (Hong et al., 2024)'
  trust: 0.95
- url: https://arxiv.org/abs/2305.18290
  title: 'Direct Preference Optimization: Your Language Model is Secretly a Reward Model (Rafailov et al., 2023)'
  trust: 0.9
---

## Definition

**ORPO does supervised fine-tuning and preference alignment in one stage, with one model.**
It is from Hong et al. (2024), titled Monolithic Preference Optimization without Reference Model.

- **Supervised fine-tuning (SFT)** trains a model to make target answers more likely, one token at
  a time.
- **The problem the authors show:** SFT on the preferred answers alone also raised the likelihood
  of the **rejected** answers (OPT-350M on HH-RLHF, their Figure 3).
- **ORPO's fix:** add a small penalty that compares the **odds** of the preferred and the rejected
  answer. Odds are P ÷ (1 − P): a probability of 0.5 gives odds of 1.
- **The figure** works one pair: the loss is **0.500** from SFT plus **0.032** from the penalty.

## How It Works

**One loss with two parts, computed by the model being trained alone.**

1. **Data:** prompts with a preferred answer y_w and a rejected answer y_l, as for DPO.
2. **Per-token average.** For each answer, average the log-probabilities of its tokens; P(y|x) is
   e to that average.
3. **Odds:** odds(y|x) = P(y|x) ÷ (1 − P(y|x)).
4. **SFT part:** the usual negative log-likelihood of the preferred answer.
5. **Odds-ratio part:** L_OR = −ln σ(ln(odds of y_w ÷ odds of y_l)); it falls as the preferred
   answer's odds pull ahead.
6. **Total:** L_SFT + λ × L_OR, with λ a small weight; the paper's released models use 0.1 to
   0.25.

**Why odds and not probabilities:** the log odds ratio has a wider range than the log probability
ratio for the same two probabilities (the paper's Figure 6).

- **The authors' argument:** a probability ratio needs a more extreme contrast to bring the loss
  down, which overly suppresses the rejected answers' tokens while the model is still adapting.
- **Their conclusion:** the odds ratio discriminates mildly and lets the preferred answers lead.

## Calculation

**The ORPO loss of one pair (equations 3 to 7).**

$$
P(y) = e^{\frac{1}{m}\sum_t \log P(y_t)}
$$

$$
\text{odds}(y) = \frac{P(y)}{1 - P(y)}
$$

$$
L_\text{OR} = -\ln \sigma\Big(\ln \frac{\text{odds}(y_w)}{\text{odds}(y_l)}\Big)
$$

$$
L_\text{ORPO} = L_\text{SFT} + \lambda \cdot L_\text{OR}
$$

| Symbol | Meaning |
| --- | --- |
| $P(y_t)$ | the probability the model gives token $y_t$, after the prompt and the tokens before it |
| $m$ | the number of tokens in the answer |
| $y_w$, $y_l$ | the preferred and the rejected answer |
| $L_\text{SFT}$ | the negative log-likelihood of the preferred answer, averaged over its tokens |
| $\lambda$ | the weight of the odds-ratio part |
| $\sigma$ | the sigmoid, σ(z) = 1 ÷ (1 + e⁻ᶻ) |

## Worked Example

**One pair with λ = 0.1.** The log-probabilities are an example, not from a trained model.

- **Preferred answer:** its tokens average a log-probability of **−0.5**.
- **Rejected answer:** its tokens average **−1.0**.

1. **Preferred:** P = e⁻⁰·⁵ ≈ **0.607**; odds = 0.607 ÷ 0.393 ≈ **1.541**.
2. **Rejected:** P = e⁻¹ ≈ **0.368**; odds = 0.368 ÷ 0.632 ≈ **0.582**.
3. **Log odds ratio:** ln(1.541 ÷ 0.582) = ln 2.649 ≈ **0.974**.
4. **Odds-ratio loss:** σ(0.974) ≈ 0.726, and −ln 0.726 ≈ **0.320**.
5. **SFT loss:** the preferred answer's negative average log-probability, **0.5**.
6. **Total:** 0.5 + 0.1 × 0.320 = 0.5 + 0.032 = **0.532**.
7. **Compare:** the log **probability** ratio of the same pair is −0.5 − (−1.0) = **0.5**, while the
   log odds ratio is already **0.974**, the wider range the authors rely on.

**What this means:** with λ = 0.1 the SFT part is most of the loss, so the model mainly learns the
preferred answers, and the penalty adds a small push away from the rejected ones.

## Memory

**One model in memory; DPO and RLHF also hold a frozen reference model.**

- **ORPO:** the model being trained only; there is no reference model.
- **DPO and RLHF:** the authors note they keep two copies of the SFT model, the one being tuned and
  a frozen reference, and need **four** forward passes per batch; ORPO needs **half** as many.
- **In the paper,** the 7B models were trained on four NVIDIA A100 GPUs with Fully Sharded Data
  Parallel (FSDP).

## Trade-offs

**The authors report strong instruction-following scores from one stage on one dataset.**

- **Mistral-ORPO-α (7B):** **11.33%** on AlpacaEval 2.0, above Zephyr β's **10.99%**, which used
  SFT then DPO; Mistral-ORPO-β reached **12.20%** on a cleaned version of the dataset.
- **Llama-2 (7B) with ORPO:** **9.44%** on AlpacaEval 2.0, above Llama-2 Chat 7B (**4.96%**) and
  13B (**7.70%**), which were trained with RLHF.
- **MT-Bench:** **7.23** and **7.32** for the two Mistral-ORPO models.
- **Scope:** models from 125M to 7B parameters, and the datasets HH-RLHF and UltraFeedback.
- **λ matters:** larger λ separated the answers more but lowered scores on tasks with exact
  answers in the ablation.

## When To Use

**Use ORPO when you want one stage from a pre-trained model.**

- **Use it** when you have preference pairs but no SFT model yet: the paper fine-tunes Phi-2,
  Llama-2 and Mistral directly.
- **Use it** when memory is tight: there is no reference model to hold.
- **Consider DPO** when you already have a good SFT model and pairs sampled from it; see
  [[fine-tuning-method/dpo]].
- **Tune λ** on your own evaluation: the paper's ablation found its effect differs by task.
