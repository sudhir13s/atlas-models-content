---
id: fine-tuning-method/qlora
type: fine-tuning-method
slug: qlora
schema_version: 1.0
title: "QLoRA (Quantized Low-Rank Adaptation)"
method_type: peft
memory_profile: low
summary: "Train LoRA adapters on top of a frozen base that has been quantized to 4-bit NF4, so a large model fits on a single consumer GPU."
when_to_use: "Fine-tune a large (7B–70B) model when VRAM is the binding constraint — a single 24GB consumer GPU — and you can accept a small quantization overhead for a huge memory saving."
related_hyperparameters: [hyperparameter/lora-r, hyperparameter/lora-alpha, hyperparameter/learning-rate]
tags: [fine-tuning, peft, qlora, quantization, lora]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2305.14314", title: "QLoRA: Efficient Finetuning of Quantized LLMs (Dettmers et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2106.09685", title: "LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)", trust: 0.9 }
---

## Definition
QLoRA keeps everything that makes [[fine-tuning-method/lora]] efficient and adds one more saving:
the frozen base model is stored in 4-bit precision instead of 16-bit. It combines three
innovations from Dettmers et al. (2023): **4-bit NormalFloat (NF4)**, a quantization type that is
information-theoretically well matched to the roughly-normal distribution of neural network weights;
**double quantization**, which also quantizes the per-block quantization constants to shave a further
~0.4 bits/parameter; and **paged optimizers**, which use NVIDIA unified memory to page optimizer
state to CPU RAM during gradient spikes rather than crashing with an out-of-memory error.

The base weights are dequantized to bf16 on the fly, block by block, only for the matmul that needs
them, then discarded — so at rest the model occupies ~4 bits/parameter. The trainable LoRA adapters
are kept in full bf16 and are the only weights that receive gradients. Remarkably, the paper shows
this 4-bit-base training matches 16-bit full fine-tuning quality on its benchmarks.

## What to look for
The memory win comes from the base: a 7B model drops from ~14GB (bf16) to ~4GB (4-bit), which is
what lets it — plus activations, adapters, and optimizer state — fit in 24GB. The LoRA knobs are
unchanged: rank `r` (see [[hyperparameter/lora-r]]), scaling `lora_alpha` (see
[[hyperparameter/lora-alpha]]), and `target_modules`. The QLoRA paper's key practical finding is to
apply LoRA to **all** linear layers (q, k, v, o *and* the FFN gate/up/down), not just q and v — with
4-bit bases the adapter cost is negligible, so wider adapter coverage recovers quality cheaply.
Watch the learning rate (see [[hyperparameter/learning-rate]]); adapters tolerate ~1e-4–2e-4. The
tradeoff versus plain LoRA is a modest throughput cost from on-the-fly dequantization.

## Calculation
The adapter parameter count is identical to LoRA: per target module `≈ 2 · r · d_model`, so
`total_trainable ≈ 2 · r · d_model · num_target_modules · num_layers`. What QLoRA changes is the
**base memory**, not the trainable count:

`base_bytes ≈ params × 4 bits / 8 = params × 0.5 bytes` (plus ~0.5 bits/param of quantization
constants before double quantization). A 7B base is thus ~3.5–4GB rather than ~14GB.

## Example
Fine-tune a 7B model (`d_model` = 4096, 32 layers) on a single 24GB GPU. Quantize the base to NF4:
`7e9 × 0.5 bytes ≈ 3.5GB`. Apply LoRA with `r = 16`, `lora_alpha = 32`, to all 7 linear projections
(q, k, v, o, gate, up, down) across 32 layers:

`2 × 16 × 4096 × 7 × 32 ≈ 29.4M` trainable parameters — about 0.42% of the 7B base. Base (~3.5GB) +
adapters + bf16 optimizer state on ~29M params (~0.35GB with paged AdamW) + activations comfortably
fits in 24GB with room for a small batch and gradient checkpointing. Pair with a paged 8-bit AdamW
and a learning rate around 2e-4. If training OOMs on a long sequence, the paged optimizer absorbs
the spike instead of crashing — the exact failure QLoRA was designed to remove.
