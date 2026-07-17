---
id: fine-tuning-method/dpo
type: fine-tuning-method
slug: dpo
schema_version: 1.0
title: "DPO (Direct Preference Optimization)"
method_type: preference
memory_profile: medium
summary: "Align a model to human preferences by optimizing a classification loss directly on chosen-vs-rejected pairs — no separate reward model and no RL loop."
when_to_use: "Preference alignment when you have paired (chosen, rejected) data and want RLHF-quality results with a simpler, more stable pipeline than PPO."
related_hyperparameters: [hyperparameter/learning-rate]
tags: [fine-tuning, preference, dpo, alignment]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2305.18290", title: "Direct Preference Optimization: Your Language Model is Secretly a Reward Model (Rafailov et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2203.02155", title: "Training language models to follow instructions with human feedback (InstructGPT, Ouyang et al., 2022)", trust: 0.9 }
---

## Definition
DPO aligns a language model to preference data without training a reward model or running
reinforcement learning. Rafailov et al. (2023) show that the RLHF objective — maximize a reward
while staying close to a reference policy under a KL penalty — has a closed-form optimum in which the
reward is expressible directly in terms of the policy's own log-probabilities. Substituting that
relationship turns the whole two-stage RLHF pipeline into a **single supervised classification loss**
on pairs of responses: raise the model's likelihood of the human-preferred ("chosen") answer and
lower it on the "rejected" answer, each measured *relative to a frozen reference model*.

Concretely, DPO minimizes
`-log σ( β · [ (log π_θ(chosen) − log π_ref(chosen)) − (log π_θ(rejected) − log π_ref(rejected)) ] )`,
where `π_θ` is the model being trained, `π_ref` is a frozen copy (usually the SFT checkpoint), `σ` is
the sigmoid, and `β` controls how far the policy may drift from the reference.

## What to look for
Two models are in memory: the trainable policy and the frozen reference (used only for forward
passes), so memory sits between plain SFT and full RLHF — hence the "medium" profile. The central
knob is **`β`** (typically ~0.1): small `β` allows larger deviation from the reference and stronger
preference fitting but risks reward hacking and degeneration; large `β` keeps the model conservative.
Use a **very low learning rate** (see [[hyperparameter/learning-rate]]): 5e-7 to 5e-6, far below SFT,
because preference gradients are sharp and the model is already competent. Start from a solid SFT
checkpoint (DPO refines behavior, it does not teach the task), watch that the *margin* between chosen
and rejected log-probs grows while both do not collapse, and monitor for the common failure where the
policy lowers the likelihood of *both* responses.

## Calculation
DPO adds no new trainable parameters — it fine-tunes the existing model, so trainable count equals
whatever base method you use (full or, very commonly, LoRA/QLoRA on top of DPO). Per training step
you run **four forward passes** — policy and reference, each on the chosen and rejected sequence — but
only the two policy passes need gradients. Memory ≈ policy training state + one frozen reference
copy (no optimizer state for the reference).

## Example
Align a 7B SFT checkpoint on a preference set of (prompt, chosen, rejected) triples. Freeze a copy as
the reference `π_ref`. Train with `β = 0.1`, learning rate `5e-6` (cosine decay, ~3% warmup), 1–2
epochs — DPO overfits quickly, so few epochs is standard. To keep memory modest, apply DPO with LoRA
adapters on the policy: the frozen base doubles as the reference (adapters off) and only the adapters
train, cutting the two-model overhead. Track the implicit-reward margin
`(log π_θ(chosen) − log π_ref(chosen)) − (log π_θ(rejected) − log π_ref(rejected))`: it should climb
steadily. If generations become terse or repetitive, raise `β` toward 0.3 to pull the policy back
toward the reference.
