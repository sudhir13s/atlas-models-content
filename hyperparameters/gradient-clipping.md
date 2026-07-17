---
id: hyperparameter/gradient-clipping
type: hyperparameter
slug: gradient-clipping
schema_version: 1.0
title: "Gradient Clipping"
name: max_grad_norm
context: training
priority: 9
importance: important
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "0.5 — 1.0 (0.0 disables)"
starting_value: "1.0"
controls: "Caps the global L2 norm of all gradients before the optimizer step, preventing runaway updates."
if_increased: "Allows larger gradient steps — faster progress but less protection against loss spikes and divergence."
if_decreased: "Tighter clipping stabilises training but can slow learning if it clips almost every step."
common_mistake: "Disabling it (or setting it very high) and then hitting NaNs on a rare bad batch — one exploding gradient can destroy a run that clipping would have saved."
tags: [training, stability, gradient]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1211.5063", title: "On the difficulty of training Recurrent Neural Networks (Pascanu et al., 2013)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2005.14165", title: "Language Models are Few-Shot Learners (GPT-3, Brown et al., 2020)", trust: 0.9 }
related: [hyperparameter/learning-rate, hyperparameter/lr-warmup-schedule]
---

## Definition
Gradient clipping rescales the gradient vector whenever its global L2 norm exceeds a threshold, so no
single step can push the weights arbitrarily far. `max_grad_norm` is that threshold.

## What to look for
`1.0` is the near-universal default for transformer training. Log the gradient norm: if it routinely sits
below the threshold, clipping rarely fires and is just insurance; if it is constantly clipped (norm ≫
threshold every step), your learning rate is probably too high or a warmup is missing. Spikes in the
gradient norm that coincide with loss spikes are exactly what clipping is there to tame.

## Calculation
Let $g$ be the concatenation of all parameter gradients and $\|g\|_2$ its global norm. Clipping scales:
$$g \leftarrow g \cdot \min\!\left(1,\; \frac{\text{max\_grad\_norm}}{\|g\|_2 + \epsilon}\right).$$
If $\|g\|_2 \le \text{max\_grad\_norm}$ the gradient is untouched; otherwise every component is scaled
down by the same factor, preserving direction and only shrinking magnitude.

## Example
Suppose on a bad batch the global gradient norm is $\|g\|_2 = 8.0$ with `max_grad_norm=1.0`. The scale
factor is $\min(1, 1.0/8.0) = 0.125$, so every gradient component is multiplied by 0.125 — the update
direction is unchanged but its size is cut to one-eighth, turning a would-be loss spike into an ordinary
step. Without clipping, that same batch at LR `3e-4` could have moved weights ~8× further than intended
and produced a NaN.
