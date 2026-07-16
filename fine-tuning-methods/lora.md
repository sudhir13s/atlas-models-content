---
id: fine-tuning-method/lora
type: fine-tuning-method
slug: lora
schema_version: 1.0
title: "LoRA (Low-Rank Adaptation)"
method_type: peft
memory_profile: low
summary: "Freeze the base weights and train small low-rank adapter matrices on the target linear layers."
when_to_use: "Default choice for style, format, and domain adaptation when you have adequate VRAM and want swappable, mergeable adapters."
related_hyperparameters: [hyperparameter/learning-rate]
tags: [fine-tuning, peft, lora, adapters]
updated: 2026-07-16
sources:
  - { url: "https://arxiv.org/abs/2106.09685", title: "LoRA: Low-Rank Adaptation of Large Language Models (Hu et al.)", trust: 0.95 }
---

## Definition
LoRA freezes the pretrained weights and injects a trainable low-rank update into selected linear
layers. For a weight matrix, it learns two small matrices A (d×r) and B (r×d); the effective update
is their product scaled by `alpha/r`. Only A and B train, so the trainable footprint is a fraction
of a percent of the base model.

## What to look for
The key knobs are the rank `r` (8–16 is a solid default; 4–8 for simple tasks, 64+ for reasoning or
code), `lora_alpha` (commonly 2×r; the effective scale is `alpha/r`), and `target_modules` (q,v is
the minimal paper default; add k,o and the FFN gate/up/down when the task changes *what the model
knows*, not just its tone). See [[hyperparameter/learning-rate]] — adapters are randomly initialised
and tolerate a higher LR (~1e-4–3e-4) than full fine-tuning.

## Calculation
Trainable parameters per target module ≈ `2 · r · d_model`. Across the chosen modules and layers:
`total_trainable ≈ 2 · r · d_model · num_target_modules · num_layers`.

## Example
On Llama 3 8B (`d_model` = 4096), r=16 on q and v projections across all 32 layers:
`2 × 16 × 4096 × 2 × 32 ≈ 8.4M` trainable parameters — about 0.1% of the 8B base. Pair with
`lora_alpha=32` (scale 2.0). If you raise r to 64 for a harder task, also raise alpha (e.g. 128) or
enable rsLoRA, otherwise the effective scale drops and the model appears to stop learning.
