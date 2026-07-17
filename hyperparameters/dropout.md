---
id: hyperparameter/dropout
type: hyperparameter
slug: dropout
schema_version: 1.0
title: "Dropout"
name: dropout
context: training
priority: 13
importance: optional
goals: [training, finetuning]
models: [llm, vision, audio, rl]
range: "0.0 — 0.1 (LLMs); up to 0.5 (small models)"
starting_value: "0.0 (large-scale pre-training); 0.05–0.1 (small-data fine-tuning)"
controls: "Probability of randomly zeroing activations during training, forcing redundant representations."
if_increased: "Stronger regularization against overfitting, but slower convergence and, at large scale, wasted capacity."
if_decreased: "Faster learning and full capacity use, but higher overfitting risk on small datasets."
common_mistake: "Adding dropout to a large-scale pre-training run — with trillions of tokens the data already regularises, and dropout just slows learning; modern LLMs set it to 0.0."
tags: [training, regularization, dropout]
updated: 2026-07-17
sources:
  - { url: "https://jmlr.org/papers/v15/srivastava14a.html", title: "Dropout: A Simple Way to Prevent Neural Networks from Overfitting (Srivastava et al., 2014)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
related: [hyperparameter/weight-decay, hyperparameter/training-duration]
---

## Definition
Dropout randomly zeros a fraction of unit activations on each forward pass during training, so the
network cannot rely on any single feature and must learn redundant, robust representations. At inference
it is turned off and activations are used at full scale.

## What to look for
The dropout probability $p$ appears as `dropout`, `attention_dropout`, or `hidden_dropout` in configs.
For **large-scale LLM pre-training**, the modern practice is `0.0` — the sheer volume of unique tokens
(trillions, seen roughly once) already prevents memorisation, so dropout only slows learning. Dropout
earns its place when data is scarce relative to capacity: small-data fine-tuning (0.05–0.1) or small
vision/RL models (up to 0.5). If train loss dives while eval loss climbs, a little dropout can help.

## Calculation
During training each activation $a_i$ is kept with probability $1-p$ and, under **inverted dropout**,
scaled up so the expected value is preserved:
$$\tilde{a}_i = \frac{m_i}{1-p}\,a_i,\qquad m_i \sim \text{Bernoulli}(1-p).$$
At inference $p=0$ and $\tilde{a}_i = a_i$, so no scaling or masking is applied.

## Example
With `dropout=0.1`, each activation has a 10% chance of being zeroed per step; the survivors are scaled
by $1/(1-0.1) = 1.111$ so the layer's expected output magnitude is unchanged. On a 2,000-example
fine-tune this suppresses memorisation and improves eval loss. On a 15-trillion-token pre-training run
(as in Llama 3), the same `0.1` would throw away 10% of every batch's signal for no regularisation
benefit — so it is set to `0.0`.
