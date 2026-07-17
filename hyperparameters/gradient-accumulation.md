---
id: hyperparameter/gradient-accumulation
type: hyperparameter
slug: gradient-accumulation
schema_version: 1.0
title: "Gradient Accumulation"
name: gradient_accumulation_steps
context: training
priority: 11
importance: important
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "1 — 64+"
starting_value: "chosen to hit the target effective batch given available memory (often 4–16)"
controls: "How many micro-batches are summed before one optimizer step — a memory-for-time lever to reach a large effective batch."
if_increased: "A larger effective batch without more memory, but more forward/backward passes per update (slower wall-clock per step)."
if_decreased: "Fewer passes per step (faster per update) but a smaller effective batch unless you add GPUs or micro-batch size."
common_mistake: "Forgetting that accumulation multiplies the effective batch — so the learning rate (tuned for the old effective batch) is now mismatched and needs rescaling."
tags: [training, memory, batch-size]
updated: 2026-07-17
sources:
  - { url: "https://huggingface.co/docs/transformers/perf_train_gpu_one", title: "Hugging Face — efficient training on a single GPU", trust: 0.85 }
  - { url: "https://arxiv.org/abs/2001.08361", title: "Scaling Laws for Neural Language Models (Kaplan et al., 2020)", trust: 0.9 }
related: [hyperparameter/effective-batch-size, hyperparameter/learning-rate]
---

## Definition
Gradient accumulation runs several micro-batches forward and backward, summing (or averaging) their
gradients, and only then takes one optimizer step. It simulates a large batch on limited memory.

## What to look for
`gradient_accumulation_steps` is the multiplier between what fits in memory (the micro-batch) and the
effective batch you actually want. Higher values let a small GPU train at a large effective batch, at
the cost of more compute per update. Because it directly scales the effective batch, it must be reasoned
about together with the learning rate and — if you use activation checkpointing or DDP — with how
gradients are averaged across accumulation steps and workers.

## Calculation
$$B_\text{eff} = \text{micro\_batch} \times \text{gradient\_accumulation\_steps} \times \text{num\_gpus}.$$
Solve for the accumulation you need to hit a target effective batch:
$$\text{gradient\_accumulation\_steps} = \frac{B_\text{eff,target}}{\text{micro\_batch}\times\text{num\_gpus}}.$$

## Example
You want an effective batch of 256 sequences but memory only allows `micro_batch=2` on a single GPU
(`num_gpus=1`). Then `gradient_accumulation_steps = 256 / (2 × 1) = 128`: the trainer accumulates 128
micro-batches (256 sequences total) before each optimizer step. Add a second GPU and it drops to
`256 / (2 × 2) = 64`. Either way the effective batch — and therefore the learning rate you should use —
is identical; only the number of forward/backward passes per step changes.
