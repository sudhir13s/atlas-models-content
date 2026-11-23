---
id: fine-tuning-method/dpo
type: fine-tuning-method
slug: dpo
schema_version: 3.1
title: DPO (Direct Preference Optimization)
method_type: preference
memory_profile: medium
summary: Train a model on pairs of a preferred and a rejected answer with one classification loss, with no separate reward model and no reinforcement-learning loop.
when_to_use: When you have prompts with a preferred and a rejected answer and want the RLHF objective without training a reward model or sampling during training.
related_hyperparameters:
- hyperparameter/learning-rate
- hyperparameter/effective-batch-size
profile:
  trains: The policy (the model being aligned)
  models_in_training: 'Two: the policy and a frozen reference model'
  data: Prompts, each with a preferred and a rejected answer
  stages: One, after supervised fine-tuning when an SFT model exists
  source: https://arxiv.org/abs/2305.18290
typical_values:
- setting: 'DPO paper: default β'
  value: '0.1'
  source: https://arxiv.org/abs/2305.18290
- setting: 'DPO paper: β for TL;DR summarization'
  value: '0.5'
  source: https://arxiv.org/abs/2305.18290
- setting: 'DPO paper: optimizer, learning rate, batch size'
  value: RMSprop, 1e-6, 64
  source: https://arxiv.org/abs/2305.18290
- setting: 'DPO paper: warmup of the learning rate from 0'
  value: 150 steps
  source: https://arxiv.org/abs/2305.18290
- setting: 'DPO paper: β values swept on the sentiment task'
  value: 0.05, 0.1, 1, 5
  source: https://arxiv.org/abs/2305.18290
- setting: 'ORPO paper: its DPO baselines (β, learning rate, epochs)'
  value: 0.1, 5e-6, 3
  source: https://arxiv.org/abs/2403.07691
mistakes:
- mistake: Dropping the weighting and just raising the preferred answer's probability against the rejected one's
  why: 'The DPO gradient weights each pair by σ(r̂(rejected) − r̂(chosen)): by **how wrongly** the implicit reward orders the two answers. The authors report that a naive version without this weighting can make the language model **degenerate** (their Appendix Table 3).'
  fix: Use the full loss of equation 7, with the reference model's log-probabilities.
- mistake: Using a reference model the preference data did not come from
  why: The preference pairs are usually sampled from the SFT model, so the paper sets π_ref = π_SFT whenever it is available. When it is not, the authors first train the reference on the preferred answers, to reduce the **distribution shift** between the data and π_ref.
  fix: Use the model that generated the pairs as the reference, or fine-tune one on the preferred answers first.
- mistake: Training many epochs
  why: In the ORPO paper's DPO baselines, trained for three epochs, the evaluation loss rose from the third epoch, and in most cases the first or second epoch's checkpoint was the best.
  fix: Evaluate after each epoch and keep the best checkpoint.
- mistake: Taking the paper's results as shown for very large models
  why: The authors evaluate models of up to **6B** parameters and list scaling DPO to models orders of magnitude larger as future work.
  fix: Check the results on your own model size and evaluation.
visual:
  kind: dpo_loss
  beta: 0.1
  policy:
    chosen: -18.0
    rejected: -21.0
  reference:
    chosen: -20.0
    rejected: -18.0
  caption: The implicit reward of each answer and the DPO loss of one pair, computed from the log-probabilities of the worked example with the paper's default β = 0.1. The log-probabilities are an example, not from a trained model.
tags:
- fine-tuning
- preference
- dpo
- alignment
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/2305.18290
  title: 'Direct Preference Optimization: Your Language Model is Secretly a Reward Model (Rafailov et al., 2023)'
  trust: 0.95
- url: https://arxiv.org/abs/2403.07691
  title: 'ORPO: Monolithic Preference Optimization without Reference Model (Hong et al., 2024)'
  trust: 0.9
- url: https://arxiv.org/abs/2203.02155
  title: Training language models to follow instructions with human feedback (Ouyang et al., 2022)
  trust: 0.9
---

## Definition

**DPO trains a language model directly on human preferences, with one loss and no reward model.**
It is from Rafailov et al. (2023), titled Direct Preference Optimization.

- **A preference pair** is a prompt with two answers, where a person marked one as better: the
  **chosen** answer and the **rejected** one.
- **The policy** is the model being trained; **the reference** is a frozen copy, usually the
  supervised fine-tuned (SFT) model.
- **The idea:** the authors show that the objective of RLHF, reinforcement learning from human
  feedback, can be met exactly with a simple **binary classification loss**.
- **The figure** works one pair: the loss falls from **0.693** at the start to **0.474** once the
  policy favours the chosen answer more than the reference does.

## How It Works

**Raise the chosen answer's probability and lower the rejected one's, each measured against the
reference.**

1. **Collect pairs.** For each prompt, two answers are sampled, usually from the SFT model, and a
   person picks the better one.
2. **Score with both models.** The policy and the frozen reference each give the log-probability
   of the chosen and of the rejected answer: four numbers per pair.
3. **Implicit reward.** For each answer, β × (log π − log π_ref): how much more the policy likes
   it than the reference does. The authors call this the reward implicitly defined by the policy.
4. **Loss.** Apply −ln σ to the chosen answer's implicit reward minus the rejected one's; σ, the
   sigmoid, squashes any number into 0 to 1.
5. **Update the policy.** The gradient weights each pair more when the implicit reward orders it
   **wrongly**, then raises the chosen answer's likelihood and lowers the rejected one's.

**Why no reward model:** RLHF maximises reward minus β times the KL divergence from the reference.
The authors show its best policy has a closed form, so the reward can be written through the
policy itself.

## Calculation

**The DPO loss of one pair (equation 7).**

In words: first each answer's implicit reward, β times the policy's log-probability minus the
reference's; then minus the log of the sigmoid of the chosen answer's reward minus the rejected
one's.

$$
\hat r(y) = \beta\,\big(\log \pi_\theta(y) - \log \pi_\text{ref}(y)\big)
$$

$$
L = -\ln \sigma\big(\hat r(y_w) - \hat r(y_l)\big)
$$

| Symbol | Meaning |
| --- | --- |
| $\hat r(y)$ | the implicit reward of answer y, as the paper names it |
| $\pi_\theta$ | the policy, the model being trained |
| $\pi_\text{ref}$ | the frozen reference model |
| $y_w$, $y_l$ | the chosen and the rejected answer to the prompt |
| $\beta$ | how strongly the policy is held near the reference; 0.1 by default in the paper |
| $\sigma$ | the sigmoid, σ(z) = 1 ÷ (1 + e⁻ᶻ) |

## Worked Example

**One pair, the paper's β = 0.1.** The log-probabilities are an example, not from a trained model.

- **Reference:** chosen **−20.0**, rejected **−18.0**: the reference slightly prefers the rejected
  answer.
- **Policy after some training:** chosen **−18.0**, rejected **−21.0**.

1. **Chosen implicit reward:** 0.1 × (−18.0 − (−20.0)) = 0.1 × 2.0 = **0.2**.
2. **Rejected implicit reward:** 0.1 × (−21.0 − (−18.0)) = 0.1 × (−3.0) = **−0.3**.
3. **Margin:** 0.2 − (−0.3) = **0.5**.
4. **Sigmoid:** σ(0.5) = 1 ÷ (1 + e⁻⁰·⁵) = 1 ÷ 1.6065 ≈ **0.622**.
5. **Loss:** −ln 0.622 ≈ **0.474**.
6. **At the start** the policy equals the reference, the margin is 0 and the loss is
   −ln σ(0) = −ln 0.5 ≈ **0.693**.
7. **Gradient weight:** σ(−0.3 − 0.2) = σ(−0.5) ≈ **0.378**, smaller than the 0.5 at the start,
   because the pair is now ordered correctly.
8. **With β = 0.5,** the paper's summarization setting, the margin is 0.5 × 5.0 = 2.5 and the loss
   −ln σ(2.5) ≈ **0.079**.

**What this means:** the loss looks only at how the policy moved **relative to the reference**,
never at the raw log-probabilities. β scales that movement: the same moves give a margin of 0.5
at β = 0.1 and 2.5 at β = 0.5.

## Memory

**Two models are involved; only one is trained.**

- **The policy** is trained, with its gradients and optimizer state.
- **The reference** is frozen and only supplies log-probabilities.
- **Forward passes:** the ORPO authors count four per batch for DPO, the policy and the reference
  each on the chosen and the rejected answers.
- **Compared with RLHF with PPO:** no reward model, no value model, and no sampling from the policy
  during training, which the DPO authors list among RLHF's costs.

## Trade-offs

**The authors report DPO at least as good as PPO-based RLHF on their tasks, with less work.**

- **Sentiment:** DPO gave the best reward for a given KL divergence of all methods tested,
  better than PPO even when PPO could use the true reward.
- **Summarization (TL;DR):** a win rate of about **61%** against reference summaries at
  temperature 0, against **57%** for PPO; in a human study DPO samples were preferred **58%** of
  the time over PPO's.
- **Dialogue (Anthropic HH):** the only computationally efficient method tested that improved on
  the dataset's preferred answers.
- **Little tuning:** the authors write that they did not meaningfully tune β, and that DPO needs
  no significant hyperparameter tuning.
- **Open questions:** out-of-distribution generalization compared with an explicit reward, and how
  reward over-optimization shows up in DPO.

## When To Use

**Use DPO when you have preference pairs and want to skip the reward model and the RL loop.**

- **Use it** when an SFT model and pairs of answers from it exist: the paper uses the SFT model as
  the reference.
- **Use it** when sampling from the model during training is too costly: DPO trains on a fixed
  dataset.
- **Consider ORPO** when there is no SFT model yet: it needs neither an SFT stage nor a reference;
  see [[fine-tuning-method/orpo]].
- **Consider RLHF with PPO** when you want to optimise a reward model on new prompts during
  training; the DPO authors list using unlabelled prompts as an open question for DPO.
