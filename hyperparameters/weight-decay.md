---
id: hyperparameter/weight-decay
type: hyperparameter
slug: weight-decay
schema_version: 1.0
title: "Weight Decay"
name: weight_decay
context: training
priority: 8
importance: important
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "0.0 — 0.1"
starting_value: "0.1 (LLM pre-training); 0.0–0.01 (fine-tuning)"
controls: "L2-style regularization that shrinks weights toward zero each step, discouraging large magnitudes."
if_increased: "Stronger regularization — smaller weights, less overfitting, but too much underfits and hurts loss."
if_decreased: "Weights grow more freely; better training fit but higher overfitting risk."
common_mistake: "Applying weight decay to bias and LayerNorm/RMSNorm parameters — these should be excluded, or you distort normalization and add no useful regularization."
tags: [training, regularization, adamw]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1711.05101", title: "Decoupled Weight Decay Regularization (Loshchilov & Hutter, 2019) — AdamW", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2005.14165", title: "Language Models are Few-Shot Learners (GPT-3, Brown et al., 2020)", trust: 0.9 }
related: [hyperparameter/learning-rate, hyperparameter/adam-beta2]
---

## Definition
Weight decay pulls each weight slightly toward zero on every step, penalising large magnitudes. In the
modern **AdamW** formulation it is decoupled from the gradient update and applied directly to the
weights, rather than folded into the loss as classic L2.

## What to look for
`weight_decay=0.1` is the standard for LLM pre-training (used by GPT-3 and most large models); fine-tunes
often use 0.0–0.01 because the model is already regularised by the pretrained prior and small data.
Crucially, check that the optimizer **excludes** bias terms and normalization (LayerNorm/RMSNorm) scales
and gains from decay — most frameworks build this parameter-group split for you, but a hand-rolled
optimizer often forgets it.

## Calculation
The AdamW update with learning rate $\eta$, weight decay $\lambda$, and Adam step $\hat{m}/(\sqrt{\hat{v}}+\epsilon)$:
$$\theta_{t+1} = \theta_t - \eta\left(\frac{\hat{m}_t}{\sqrt{\hat{v}_t}+\epsilon} + \lambda\,\theta_t\right).$$
The $\lambda\theta_t$ term is the decay: each step also removes a fraction $\eta\lambda$ of the current
weight, independent of the gradient.

## Example
With $\eta = 3\text{e-}4$ and $\lambda = 0.1$, a weight of magnitude 0.5 is shrunk each step by
$\eta\lambda\theta = 3\text{e-}4 \times 0.1 \times 0.5 = 1.5\text{e-}5$ on top of its gradient update — a
per-step pull of $\eta\lambda = 3\text{e-}5$ (0.003%) toward zero. Small per step, but over 140,000 steps
it meaningfully caps weight growth. Set `weight_decay=0.0` for the bias and RMSNorm groups so those
parameters keep their learned scales.
