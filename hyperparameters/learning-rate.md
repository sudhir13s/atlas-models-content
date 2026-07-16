---
id: hyperparameter/learning-rate
type: hyperparameter
slug: learning-rate
schema_version: 1.0
title: "Learning Rate"
name: learning_rate
context: training
priority: 1
importance: critical
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "1e-5 — 3e-4"
starting_value: "3e-4 (pre-training a 7B), 2e-5 (full fine-tune), 2e-4 (LoRA)"
controls: "Step size of gradient descent — how far weights move per update; the single most impactful training knob."
if_increased: "Faster initial learning, but risk of loss spikes, NaNs, and divergence."
if_decreased: "More stable, smoother convergence, but slower and can stall in poor minima."
common_mistake: "Reusing the pre-training LR (3e-4) when fine-tuning a trained model → catastrophic forgetting. Drop 10–100×."
tags: [training, optimization, learning-rate]
updated: 2026-07-16
sources:
  - { url: "https://arxiv.org/abs/2001.08361", title: "Scaling Laws for Neural Language Models (Kaplan et al.)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
---

## Definition
The learning rate scales each gradient step. It is the highest-leverage training hyperparameter and
interacts tightly with the effective batch size and the warmup schedule.

## What to look for
Watch the gradient norm and the gap between training and evaluation loss. A gradient norm that
stays high (or spikes) signals the LR is too high; a loss that barely moves signals it is too low.
Typical values: pre-training a 7B ~1e-4–3e-4; full fine-tuning 1e-5–2e-5; LoRA 1e-4–3e-4 (randomly
initialised adapters tolerate a higher rate).

## Calculation
When you change the effective batch size, rescale the LR. The safe default is **square-root
scaling**: $\text{LR}_\text{new} = \text{LR}_\text{base} \cdot \sqrt{B_\text{new}/B_\text{base}}$.
Linear scaling ($\text{LR} \propto B$) is more aggressive and used mainly for very large batches.
Effective batch = `micro_batch × grad_accum × num_gpus`.

## Example
A base run uses 3e-4 at an effective batch of 256. Moving to batch 512 → `3e-4 × √2 ≈ 4.2e-4`.
Now fine-tune that same 8B model on your own data: drop to `2e-5` (10–100× lower) or the model will
overwrite its pretrained knowledge (catastrophic forgetting).
