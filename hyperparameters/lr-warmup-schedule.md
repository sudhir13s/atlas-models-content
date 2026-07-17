---
id: hyperparameter/lr-warmup-schedule
type: hyperparameter
slug: lr-warmup-schedule
schema_version: 1.0
title: "LR Warmup & Schedule"
name: warmup_ratio
context: training
priority: 4
importance: critical
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "0.0 — 0.05 (warmup fraction of total steps)"
starting_value: "0.01–0.03 warmup, then cosine decay to ~10% of peak LR"
controls: "How the learning rate ramps up from ~0 to its peak, then decays over the run."
if_increased: "A longer warmup further protects early steps from loss spikes, but wastes budget at a tiny LR if overdone."
if_decreased: "Reaches peak LR sooner (more early progress) but risks divergence in the first hundreds of steps when Adam's variance estimates are still cold."
common_mistake: "No warmup at all with a high peak LR → an immediate loss spike or NaN in the first few steps, because Adam's second-moment estimate has not yet stabilised."
tags: [training, learning-rate, scheduler, warmup]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2001.08361", title: "Scaling Laws for Neural Language Models (Kaplan et al., 2020)", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/main_classes/optimizer_schedules", title: "Hugging Face — optimizer schedules", trust: 0.85 }
related: [hyperparameter/learning-rate, hyperparameter/adam-beta2]
---

## Definition
The warmup-and-decay schedule shapes the learning rate over time: a short linear ramp from near-zero to
the peak LR (warmup), followed by a decay — most commonly cosine — down toward a small final value.

## What to look for
`warmup_ratio` (or `warmup_steps`) sets the ramp length; `lr_scheduler_type` names the decay shape
(`cosine`, `linear`, `constant_with_warmup`). Warmup of 1–3% of total steps is standard for LLM
pre-training. Cosine decay to about 10% of the peak LR is the common default; letting it decay fully to
zero is fine for a fixed-length run but makes it hard to resume or extend. If you see early loss spikes,
the warmup is too short or the peak LR too high.

## Calculation
Warmup steps from the ratio: $\text{warmup\_steps} = \lceil \text{warmup\_ratio} \times \text{max\_steps}\rceil$.
During warmup the LR rises linearly: $\text{LR}(t) = \text{LR}_\text{peak}\cdot t/\text{warmup\_steps}$.
After warmup, cosine decay over the remaining $T$ steps:
$$\text{LR}(t) = \text{LR}_\text{min} + \tfrac{1}{2}(\text{LR}_\text{peak}-\text{LR}_\text{min})\left(1+\cos\frac{\pi\,(t-\text{warmup\_steps})}{T}\right).$$

## Example
For a 140,000-step pre-training run with peak LR `3e-4`, `warmup_ratio=0.02` gives `warmup_steps =
0.02 × 140{,}000 = 2{,}800`. The LR climbs linearly from 0 to `3e-4` over the first 2,800 steps, then
cosine-decays over the remaining 137,200 steps to `LR_min = 3e-5` (10% of peak). At the midpoint of the
decay (step ~71,400) the cosine term is 0, so the LR is `(3e-4 + 3e-5)/2 ≈ 1.65e-4`.
