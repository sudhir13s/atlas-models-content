---
id: fine-tuning-method/full-fine-tuning
type: fine-tuning-method
slug: full-fine-tuning
schema_version: 1.0
title: "Full Fine-Tuning"
method_type: full
memory_profile: high
summary: "Update every weight in the model with backprop — the maximum-capacity, maximum-cost adaptation method."
when_to_use: "Reach for it when you need deep behavioral change or knowledge injection that adapters cannot express, have the VRAM (or multi-GPU sharding) for full optimizer state, and a large, high-quality dataset."
related_hyperparameters: [hyperparameter/learning-rate, hyperparameter/weight-decay]
tags: [fine-tuning, full, sft]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1810.04805", title: "BERT: Pre-training of Deep Bidirectional Transformers (Devlin et al., 2018)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2109.01903", title: "The Power of Scale for Parameter-Efficient Prompt Tuning (Lester et al., 2021)", trust: 0.85 }
  - { url: "https://arxiv.org/abs/1910.02054", title: "ZeRO: Memory Optimizations Toward Training Trillion Parameter Models (Rajbhandari et al., 2019)", trust: 0.9 }
---

## Definition
Full fine-tuning continues training the entire pretrained model on your task data: every parameter
receives a gradient and is updated. It is the original transfer-learning recipe (the way BERT was
adapted to downstream tasks) and remains the highest-capacity option because nothing is frozen — the
model can move any weight in any direction the loss demands. The cost is that you must store, and
compute gradients and optimizer state for, all of the parameters, which makes it the most
memory-hungry method by a wide margin.

## What to look for
The dominant cost is optimizer state, not the weights themselves. With Adam/AdamW in mixed
precision you pay, per parameter: 2 bytes (bf16 weight) + 4 bytes (fp32 master weight) + 4 bytes
(fp32 momentum) + 4 bytes (fp32 variance) ≈ **16 bytes/parameter**, plus activations. That is why a
7B model needs ~112GB just for model+optimizer state — full fine-tuning at scale requires sharding
(ZeRO/FSDP) across multiple GPUs. The critical hyperparameter is a **small learning rate** (see
[[hyperparameter/learning-rate]]): 1e-5 to 2e-5 is the safe band, because large updates to
already-good pretrained weights cause catastrophic forgetting. Use warmup plus cosine decay, and
[[hyperparameter/weight-decay]] (~0.01–0.1, excluding norms and biases) to regularize. Watch for
overfitting and forgetting: on small datasets, an adapter method (LoRA/QLoRA) is usually the better
default; reserve full fine-tuning for deep behavior change or distillation into the base.

## Calculation
Trainable parameters = the full parameter count, `P`. Peak training memory with mixed-precision
AdamW is approximately:

`memory ≈ 16 · P bytes (weights + master copy + Adam moments) + activations + gradients`

Sharding with ZeRO-3 / FSDP divides the 16·P optimizer+weight term by the number of GPUs, which is
what makes billion-parameter full fine-tuning feasible.

## Example
Full fine-tune a 7B model (`P` = 7e9). Optimizer + weight memory `≈ 16 × 7e9 = 112GB` — impossible
on a single 80GB GPU, so shard across 2× A100 80GB with FSDP (each holds ~56GB of state) plus
activation checkpointing to fit the forward activations. Set learning rate `1.5e-5`, warmup 3% of
steps then cosine decay to ~10% of peak, `weight_decay = 0.1` (excluding LayerNorm and bias
parameters), global batch ~256 sequences via gradient accumulation. Contrast with QLoRA, which
adapts the *same* 7B on a single 24GB GPU — full fine-tuning spends ~30× the memory to gain the
freedom to move every weight, justified only when the task truly needs it.
