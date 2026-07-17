---
id: hyperparameter/lora-alpha
type: hyperparameter
slug: lora-alpha
schema_version: 1.0
title: "LoRA Alpha"
name: lora_alpha
context: training
priority: 6
importance: important
goals: [finetuning]
models: [llm, vision, audio, diffusion]
range: "8 — 128 (commonly 1× or 2× the rank r)"
starting_value: "2 × lora_r (e.g. r=16 → alpha=32)"
controls: "The scaling factor applied to the LoRA update; the adapter contributes ΔW·(alpha/r) to the frozen weights."
if_increased: "A stronger adapter signal (larger effective update) — faster adaptation but more risk of instability and overfitting."
if_decreased: "A gentler update that stays closer to the base model; safer but may underfit."
common_mistake: "Changing r without changing alpha (or vice-versa) and being surprised the effective strength shifted — with classic LoRA the scale is alpha/r, so they move together."
tags: [finetuning, lora, peft, scaling]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2106.09685", title: "LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2312.03732", title: "A Rank Stabilization Scaling Factor for Fine-Tuning with LoRA (Kalajdzievski, 2023)", trust: 0.85 }
related: [hyperparameter/lora-r, hyperparameter/target-modules]
---

## Definition
`lora_alpha` is a fixed scalar that scales the low-rank update before it is added to the frozen weights.
The effective contribution is $\Delta W \cdot (\alpha / r)$, so alpha decouples the update's *magnitude*
from its *rank*.

## What to look for
Read alpha only relative to `r`, because what matters is the ratio $\alpha/r$. A common convention is
`alpha = 2r` (scale 2.0) or `alpha = r` (scale 1.0). If you raise `r` for more capacity but keep the
same effective strength, scale alpha proportionally. For higher ranks, **rsLoRA** changes the divisor
to $\sqrt{r}$ (scale $\alpha/\sqrt{r}$), which keeps the update magnitude stable as rank grows and
often trains better at large `r`.

## Calculation
Classic LoRA scaling of the update:
$$W_\text{eff} = W + \frac{\alpha}{r}\,BA.$$
Rank-stabilised LoRA (rsLoRA):
$$W_\text{eff} = W + \frac{\alpha}{\sqrt{r}}\,BA.$$

## Example
With `r=16` and `alpha=32`, the classic scale is $\alpha/r = 32/16 = 2.0$ — the adapter's raw update is
doubled before being added. Now bump capacity to `r=64` but keep `alpha=32`: the classic scale drops to
$32/64 = 0.5$, quietly weakening the adapter 4× versus your `r=16` run. To hold strength, set
`alpha=128` (`128/64 = 2.0`). Under rsLoRA at `r=64`, the scale would instead be $32/\sqrt{64} = 32/8 =
4.0$, which stays well-behaved as you push the rank higher.
