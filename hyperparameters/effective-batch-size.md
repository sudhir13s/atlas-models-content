---
id: hyperparameter/effective-batch-size
type: hyperparameter
slug: effective-batch-size
schema_version: 1.0
title: "Effective Batch Size"
name: effective_batch_size
context: training
priority: 2
importance: critical
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "0.5M — 4M tokens/step (LLM pre-training); 32 — 512 samples (fine-tuning)"
starting_value: "~1M tokens/step (7B pre-training); 64–256 samples (fine-tuning)"
controls: "How many samples (or tokens) contribute to one gradient update — the signal-to-noise ratio of each step."
if_increased: "Smoother, lower-variance gradients and better hardware utilisation, but each step sees fewer updates per token and may need a higher learning rate."
if_decreased: "Noisier gradients (a mild regulariser) and more frequent updates, but unstable at very small sizes and slower wall-clock throughput."
common_mistake: "Reporting only the micro-batch and forgetting that gradient accumulation and multiple GPUs multiply it — the true effective batch (and the LR it needs) is far larger."
tags: [training, batch-size, optimization]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2001.08361", title: "Scaling Laws for Neural Language Models (Kaplan et al., 2020)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
related: [hyperparameter/learning-rate, hyperparameter/gradient-accumulation, hyperparameter/adam-beta2]
---

## Definition
The effective batch size is the total number of samples — or, for language models, the total number of
tokens — that contribute to a single optimizer step, after multiplying the per-device micro-batch by
gradient accumulation and the number of data-parallel workers.

## What to look for
For LLMs, always convert to **tokens per step**, because that is the unit scaling laws are written in
and the number that determines learning-rate scaling. A config showing `micro_batch=4` looks tiny, but
with `grad_accum=8`, `seq_len=4096`, and 8 GPUs it is `4 × 8 × 8 × 4096 ≈ 1.05M` tokens/step — a
healthy pre-training batch. Very small effective batches (a few thousand tokens) give noisy gradients
and unstable loss; very large ones (many millions) waste compute past the "critical batch size" where
extra parallelism stops improving per-step progress.

## Calculation
$$B_\text{eff} = \text{micro\_batch} \times \text{grad\_accum} \times \text{num\_gpus}$$
For token-based reporting, multiply by the sequence length:
$$\text{tokens/step} = B_\text{eff} \times \text{seq\_len}.$$
When you change $B_\text{eff}$, rescale the learning rate — the safe default is square-root scaling,
$\text{LR}_\text{new} = \text{LR}_\text{base}\sqrt{B_\text{new}/B_\text{base}}$ (see
[[hyperparameter/learning-rate]]).

## Example
You have 8 A100s. Memory allows `micro_batch=2` at `seq_len=4096`. To reach ~1M tokens/step you need
`grad_accum = 1{,}000{,}000 / (2 × 8 × 4096) ≈ 16`. So set `gradient_accumulation_steps=16`, giving an
effective batch of `2 × 16 × 8 = 256` sequences = `256 × 4096 ≈ 1.05M` tokens/step. If you later drop
to 4 GPUs, double `grad_accum` to 32 to hold the same effective batch — otherwise the LR you tuned no
longer matches the gradient noise.
