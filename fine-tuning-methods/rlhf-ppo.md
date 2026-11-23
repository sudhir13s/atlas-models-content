---
id: fine-tuning-method/rlhf-ppo
type: fine-tuning-method
slug: rlhf-ppo
schema_version: 3.1
title: RLHF with PPO
method_type: rlhf
memory_profile: high
summary: Train a reward model on people's rankings of answers, then improve the language model with PPO to earn more reward, minus a penalty for drifting from the supervised model.
when_to_use: 'When you want to optimise a learned reward on new prompts during training and can run four models: the policy, a frozen reference, the reward model and a value model.'
related_hyperparameters:
- hyperparameter/learning-rate
- hyperparameter/effective-batch-size
profile:
  trains: The policy, and a value model that estimates expected reward
  models_in_training: 'Four: policy and value model (trained), SFT reference and reward model (frozen)'
  data: Written demonstrations, rankings of sampled answers, and prompts without answers
  stages: 'Three: supervised fine-tuning, reward model, PPO'
  source: https://arxiv.org/abs/2203.02155
typical_values:
- setting: 'InstructGPT: KL reward coefficient β'
  value: '0.02'
  source: https://arxiv.org/abs/2203.02155
- setting: 'InstructGPT: pre-training coefficient γ (PPO-ptx; 0 for plain PPO)'
  value: '27.8'
  source: https://arxiv.org/abs/2203.02155
- setting: 'InstructGPT: PPO clip ratio; batch size; minibatch size'
  value: 0.2; 512; 64
  source: https://arxiv.org/abs/2203.02155
- setting: 'InstructGPT: episodes; unique prompts'
  value: 256k; about 31k
  source: https://arxiv.org/abs/2203.02155
- setting: 'InstructGPT: reward model size; learning rate; epochs'
  value: 6B; 9e-6; 1
  source: https://arxiv.org/abs/2203.02155
- setting: 'InstructGPT: value model learning rate, 1.3B and 6B policies / 175B policy'
  value: 9e-6 / 5e-6
  source: https://arxiv.org/abs/2203.02155
- setting: 'InstructGPT: policy learning rates scanned for 1.3B and 6B models'
  value: 2.55e-6 to 2.55e-5
  source: https://arxiv.org/abs/2203.02155
- setting: 'Ziegler et al.: target KL, descriptiveness / sentiment tasks (GPT-2 774M)'
  value: 6 nats / 10 nats
  source: https://arxiv.org/abs/1909.08593
- setting: 'PPO paper: clip ε compared on its benchmark (best score at 0.2)'
  value: 0.1, 0.2, 0.3
  source: https://arxiv.org/abs/1707.06347
mistakes:
- mistake: Leaving out the KL penalty
  why: Ziegler et al. show a model fine-tuned without it producing **gibberish** that still scores well, even with an entropy bonus (their Table 10). InstructGPT's labelers rated models trained with a KL coefficient of **0** and of **2** poorly; the best values were about **0.01 to 0.02**.
  fix: Keep the penalty; InstructGPT's β = 0.02 is a published starting point.
- mistake: Raising β to stop losses on other benchmarks
  why: InstructGPT's PPO models lost ground on public datasets such as SQuADv2 and DROP. Raising the KL coefficient to **2.0**, 100 times the default, still did not fix these losses.
  fix: Mix pre-training updates into PPO, as InstructGPT's PPO-ptx does with γ = 27.8.
- mistake: Using too large a learning rate for the policy
  why: In InstructGPT's scan for 1.3B and 6B models, every PPO run without the pre-training mix **diverged** at learning rates above **8.05e-6**; with the mix, training was less sensitive to the learning rate.
  fix: Scan small learning rates and judge them by human or reward-model evaluation.
- mistake: Training the reward model for several epochs
  why: InstructGPT's reward model overfit quickly when trained for more than one epoch, and Ziegler et al. also use a single epoch to avoid overfitting their small amount of human data.
  fix: Train the reward model for one epoch over the comparisons.
visual:
  kind: kl_reward
  score: 1.5
  betas:
  - 0.02
  - 2.0
  policy:
  - 0.5
  - 0.3
  - 0.8
  - 0.9
  reference:
  - 0.25
  - 0.4
  - 0.8
  - 0.6
  caption: 'One four-token answer: ln(policy ÷ reference) at each token, and the reward model''s score of 1.5 minus β times their sum, for InstructGPT''s β = 0.02 and for 2.0, 100 times larger, which its authors also tested. The probabilities and the score are an example, not from a trained model.'
tags:
- fine-tuning
- rlhf
- ppo
- alignment
- reward-model
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/2203.02155
  title: Training language models to follow instructions with human feedback (InstructGPT, Ouyang et al., 2022)
  trust: 0.95
- url: https://arxiv.org/abs/1707.06347
  title: Proximal Policy Optimization Algorithms (Schulman et al., 2017)
  trust: 0.9
- url: https://arxiv.org/abs/1909.08593
  title: Fine-Tuning Language Models from Human Preferences (Ziegler et al., 2019)
  trust: 0.9
- url: https://arxiv.org/abs/1910.02054
  title: 'ZeRO: Memory Optimizations Toward Training Trillion Parameter Models (Rajbhandari et al., 2019)'
  trust: 0.9
- url: https://arxiv.org/abs/2305.18290
  title: 'Direct Preference Optimization: Your Language Model is Secretly a Reward Model (Rafailov et al., 2023)'
  trust: 0.9
---

## Definition

**RLHF with PPO teaches a model from people's judgements in three stages.**
RLHF is reinforcement learning from human feedback; PPO, proximal policy optimization, is the
reinforcement-learning algorithm. The recipe on this page is InstructGPT's (Ouyang et al., 2022).

- **The policy** is the model being trained; its answers are its actions.
- **A reward model** reads a prompt and an answer and outputs one number, learned from people's
  rankings.
- **The KL penalty** (Kullback-Leibler divergence) charges the policy for moving away from the
  supervised model; the figure shows it on one answer.
- **The method as a whole** is described on the architecture page
  [RLHF & Instruction Alignment](/models/architecture/rlhf); this page covers running it.

## How It Works

**Supervised fine-tuning, then a reward model, then PPO against that reward.**

1. **Supervised fine-tuning (SFT).** The pre-trained model is fine-tuned on answers written by
   labelers. This SFT model is the starting point of the policy and stays frozen as the
   **reference**.
2. **Rankings.** For each prompt, 4 to 9 sampled answers are ranked by a labeler; K answers give
   K(K − 1) ÷ 2 pairs.
3. **Reward model.** A **6B** model learns to score the preferred answer of each pair higher, with
   the loss −ln σ(r(preferred) − r(other)). InstructGPT initialised it from a 6B GPT-3 fine-tuned
   on public NLP datasets, for historical reasons; the authors found similar results starting
   from GPT-3 or the SFT model.
4. **Normalise.** A bias is added so that labeler demonstrations score **0** on average.
5. **Episodes.** The policy answers a prompt; the reward model scores the answer.
6. **Penalty.** At each token, β × ln(policy ÷ reference) is subtracted, with β = **0.02**.
7. **Advantage.** A **value model**, initialised from the reward model, estimates the expected
   reward; the advantage is how much better the answer did than expected.
8. **PPO update.** The policy is moved to raise the penalised reward, but each update is
   **clipped**: the ratio of new to old probability earns nothing beyond 1 ± 0.2.
9. **PPO-ptx** (optional). Next-token updates on pre-training data are added, weighted by
   γ = **27.8**; InstructGPT means this variant.

## Calculation

**The reward PPO maximises for one answer** (InstructGPT's equation 2, first part).

In words: the reward model's score, minus β times the sum over the answer's tokens of the log of
how much more likely the policy makes each token than the reference does.

$$
R = r_\theta(x, y) - \beta \sum_t \ln \frac{\pi^\text{RL}(y_t)}{\pi^\text{SFT}(y_t)}
$$

**PPO's clipped objective for one step** (Schulman et al., equation 7).

$$
L = \min\big(\rho A,\ \operatorname{clip}(\rho, 1 - \epsilon, 1 + \epsilon)\,A\big)
$$

| Symbol | Meaning |
| --- | --- |
| $r_\theta(x, y)$ | the reward model's score of answer y to prompt x |
| $\pi^\text{RL}(y_t)$, $\pi^\text{SFT}(y_t)$ | the probability the policy and the frozen supervised model give token $y_t$ of the answer, after the prompt and the tokens before it |
| $\beta$ | the KL coefficient; 0.02 in InstructGPT |
| $\rho$ | new probability ÷ old probability of the action |
| $A$ | the advantage |
| $\epsilon$ | the clip range; 0.2 in InstructGPT and in the PPO paper's best setting |

## Worked Example

**One answer's reward, then one clipped step.** All numbers are an example, not from a trained
model, except β and ε, which are InstructGPT's.

**Part 1: the KL penalty** (what the figure draws). The reward model scores the answer **1.5**.

| Token | Policy | Reference | ln(policy ÷ reference) |
| --- | --- | --- | --- |
| 1 | 0.5 | 0.25 | ln 2 ≈ 0.6931 |
| 2 | 0.3 | 0.4 | ln 0.75 ≈ −0.2877 |
| 3 | 0.8 | 0.8 | ln 1 = 0 |
| 4 | 0.9 | 0.6 | ln 1.5 ≈ 0.4055 |

1. **Sum:** 0.6931 − 0.2877 + 0 + 0.4055 = **0.8109**. It equals ln(0.108 ÷ 0.048) = ln 2.25, the
   ratio of the whole answer's probabilities (0.5 × 0.3 × 0.8 × 0.9 and 0.25 × 0.4 × 0.8 × 0.6).
2. **β = 0.02:** penalty 0.02 × 0.8109 ≈ 0.0162; reward 1.5 − 0.0162 ≈ **1.4838**.
3. **β = 2.0:** penalty 2.0 × 0.8109 ≈ 1.6219; reward 1.5 − 1.6219 ≈ **−0.1219**.

**Part 2: the clip for a bad answer.** Suppose the answer did worse than expected, an advantage
of **−1.0**, and an update would lower its probability ratio to **0.7**.

1. **Unclipped:** 0.7 × (−1.0) = −0.7.
2. **Clipped** to 1 − 0.2 = 0.8: 0.8 × (−1.0) = −0.8.
3. **PPO keeps the smaller:** **−0.8**. Pushing the ratio below 0.8 earns nothing more.

**What this means:** a token the policy likes less than the reference lowers the penalty, so the
sum can be small even after real change; β decides how much that change costs, and the clip
limits how far one batch can move the policy in either direction.

## Memory

**Four models take part, two of them trained.** InstructGPT used a 6B reward model and a 6B value
model for policies of every size.

- **Trained:** the policy and the value model, each with gradients and optimizer state.
- **Frozen:** the SFT reference and the reward model, which only score.
- **An estimate for the 1.3B policy,** using ZeRO's accounting of mixed-precision Adam (16 bytes
  per trained weight, 2 per frozen 16-bit weight); InstructGPT does not report its memory:
  1. **Policy:** 1.3 × 10⁹ × 16 = **20.8 GB**.
  2. **Value model:** 6 × 10⁹ × 16 = **96 GB**.
  3. **Reference:** 1.3 × 10⁹ × 2 = **2.6 GB**.
  4. **Reward model:** 6 × 10⁹ × 2 = **12 GB**.
  5. **Total:** **131.4 GB**, before activations and the memory for generating answers.
- **The authors' reasons for a 6B reward model:** 175B reward models trained less stably, which
  made them worse starting points for the value model, and greatly increased PPO's compute.

## Trade-offs

**InstructGPT shows large gains from RLHF, at the cost of a complex pipeline.**

- **Preference:** labelers preferred the **1.3B** InstructGPT to the **175B** GPT-3; the 175B
  InstructGPT was preferred to the 175B GPT-3 **85 ± 3%** of the time.
- **Alignment tax:** PPO lost ground on public datasets such as SQuADv2 and DROP; the pre-training
  mix reduced it.
- **Complexity:** the DPO authors describe RLHF as complex and often unstable, training several
  language models and sampling from the policy during training.
- **Reward over-optimisation:** the KL penalty exists, in InstructGPT's words, to mitigate
  over-optimisation of the reward model.

## When To Use

**Use RLHF with PPO when you need to optimise a learned reward on new prompts.**

- **Use it** when the reward model must judge answers the policy writes during training, on
  prompts without answers: InstructGPT's PPO stage used about 31k such prompts.
- **Use it** when you can run four models and three stages.
- **Consider DPO** when a fixed set of preference pairs is enough: it optimises the same objective
  without a reward model or sampling; see [[fine-tuning-method/dpo]].
- **Consider ORPO** to also skip supervised fine-tuning and the reference model; see
  [[fine-tuning-method/orpo]].
