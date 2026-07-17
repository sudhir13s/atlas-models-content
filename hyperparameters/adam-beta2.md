---
id: hyperparameter/adam-beta2
type: hyperparameter
slug: adam-beta2
schema_version: 1.0
title: "Adam Beta2"
name: adam_beta2
context: training
priority: 12
importance: optional
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "0.95 — 0.999"
starting_value: "0.95 (large-batch LLM pre-training); 0.999 (default / fine-tuning)"
controls: "The exponential decay rate for Adam's second-moment (variance) estimate — how much gradient history the adaptive step size uses."
if_increased: "Toward 0.999 averages over more steps — smoother variance estimate but slower to react to changing gradient scale."
if_decreased: "Toward 0.95 shortens the memory — reacts faster to gradient-scale changes, which stabilises large-batch LLM training."
common_mistake: "Leaving beta2 at the library default 0.999 for large-batch pre-training, where the long variance memory reacts too slowly and allows loss spikes — GPT-3 and others use 0.95."
tags: [training, optimizer, adam]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1412.6980", title: "Adam: A Method for Stochastic Optimization (Kingma & Ba, 2015)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2005.14165", title: "Language Models are Few-Shot Learners (GPT-3, Brown et al., 2020)", trust: 0.9 }
related: [hyperparameter/learning-rate, hyperparameter/effective-batch-size]
---

## Definition
Adam keeps a running estimate of the gradient's second moment (uncentered variance) to normalise each
parameter's step size. `adam_beta2` is the exponential-moving-average decay for that estimate — how long
its memory is.

## What to look for
The library default is `0.999`, effectively averaging over the last ~1000 gradients. For **large-batch
LLM pre-training**, practitioners lower it to `0.95` (memory ~20 steps) so the variance estimate adapts
faster when the gradient scale shifts, which reduces loss spikes. For fine-tuning and smaller-batch
runs, `0.999` is fine. `beta1` (the first-moment/momentum decay) is almost always left at 0.9.

## Calculation
The second-moment EMA over gradients $g_t$:
$$v_t = \beta_2\, v_{t-1} + (1-\beta_2)\, g_t^2.$$
The effective averaging window is about $1/(1-\beta_2)$ steps. The step then uses the bias-corrected
$\hat{v}_t = v_t/(1-\beta_2^t)$ in $\theta \mathrel{-}= \eta\,\hat{m}_t/(\sqrt{\hat{v}_t}+\epsilon)$.

## Example
At $\beta_2 = 0.999$, the window is $1/(1-0.999) = 1000$ steps — a slow, smooth variance estimate. Drop
to $\beta_2 = 0.95$ and the window shrinks to $1/(1-0.95) = 20$ steps, so a sudden 3× jump in gradient
magnitude is reflected in the denominator within a handful of steps instead of hundreds — the adaptive
step size shrinks quickly, damping the spike. This is exactly why GPT-3-scale pre-training runs set
`adam_beta2=0.95`.
