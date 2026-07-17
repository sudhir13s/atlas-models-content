---
id: hyperparameter/training-duration
type: hyperparameter
slug: training-duration
schema_version: 1.0
title: "Training Duration"
name: max_steps
context: training
priority: 3
importance: critical
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "1 — 10 epochs (fine-tuning); billions to trillions of tokens (pre-training)"
starting_value: "~20 tokens/parameter (Chinchilla-optimal pre-training); 1–3 epochs (fine-tuning)"
controls: "The total token/step budget the optimizer runs for — how much the model gets to learn before the run ends."
if_increased: "More learning and lower loss up to a point, then diminishing returns and eventual overfitting on small datasets."
if_decreased: "Cheaper and faster, but an undertrained model that leaves capability on the table."
common_mistake: "Treating Chinchilla's 20 tokens/param as a ceiling — for inference-heavy models you deliberately over-train (Llama 3 used ~15T tokens for an 8B model, ~1875 tokens/param)."
tags: [training, scaling-laws, compute-budget]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2203.15556", title: "Training Compute-Optimal Large Language Models (Hoffmann et al., 2022)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
related: [hyperparameter/effective-batch-size, hyperparameter/lr-warmup-schedule]
---

## Definition
Training duration is the total amount of optimization the run performs, expressed either as a number of
optimizer steps (`max_steps`), epochs over a dataset, or — the most portable unit for LLMs — a total
token budget.

## What to look for
In a config, `max_steps` and `num_train_epochs` are two ways to express the same thing; one usually
overrides the other. For fine-tuning, 1–3 epochs is typical and more than ~5 epochs on a small dataset
usually overfits (watch eval loss turn upward). For pre-training, the meaningful figure is total
tokens: the Chinchilla compute-optimal ratio is ~20 training tokens per parameter, but modern models
are deliberately **over-trained** to make inference cheaper for a given quality.

## Calculation
Chinchilla's rule for a fixed compute budget: for $N$ parameters, use roughly $D \approx 20N$ training
tokens. Convert a token budget to steps via the effective batch:
$$\text{max\_steps} = \frac{D_\text{target}}{\text{tokens/step}} = \frac{D_\text{target}}{B_\text{eff}\times\text{seq\_len}}.$$
Over-training simply chooses $D \gg 20N$ (e.g. Llama 3 8B at ~15T tokens ≈ $1875N$).

## Example
Pre-training a 7B model Chinchilla-optimally needs $D \approx 20 \times 7\text{e}9 = 1.4\text{e}11$ =
140B tokens. At ~1M tokens/step that is `max_steps = 1.4e11 / 1e6 = 140{,}000` steps. If instead you
want an inference-optimised model and budget 2T tokens, `max_steps = 2e12 / 1e6 = 2{,}000{,}000` steps
— the same architecture, roughly 14× the compute, for a smaller, faster, higher-quality deployed model.
