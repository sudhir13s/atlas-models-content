---
id: fine-tuning-method/orpo
type: fine-tuning-method
slug: orpo
schema_version: 1.0
title: "ORPO (Odds Ratio Preference Optimization)"
method_type: preference
memory_profile: medium
summary: "Combine supervised fine-tuning and preference alignment into one loss, using an odds-ratio penalty — no reference model and no separate preference stage."
when_to_use: "When you want a single-stage recipe that teaches the task and aligns to preferences at once, saving the extra SFT pass and the reference-model memory that DPO and RLHF require."
related_hyperparameters: [hyperparameter/learning-rate]
tags: [fine-tuning, preference, orpo, alignment]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2403.07691", title: "ORPO: Monolithic Preference Optimization without Reference Model (Hong et al., 2024)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2305.18290", title: "Direct Preference Optimization (Rafailov et al., 2023)", trust: 0.9 }
---

## Definition
ORPO (Hong et al., 2024) folds supervised fine-tuning and preference alignment into a **single
training stage with no reference model**. Its loss is the standard SFT cross-entropy on the chosen
response, plus an **odds-ratio** term that penalizes the model for assigning high odds to the rejected
response relative to the chosen one:

`L_ORPO = L_SFT(chosen) + λ · L_OR`, where
`L_OR = −log σ( log[ odds_θ(chosen) / odds_θ(rejected) ] )` and `odds(y) = p(y) / (1 − p(y))`.

Because the penalty is built from the model's *own* odds — not a divergence from a frozen reference —
ORPO needs only one model in memory. The cross-entropy term keeps the model fluent and on-task while
the odds-ratio term pushes probability mass away from dispreferred outputs. This removes both the
separate SFT phase that DPO assumes and the reference-model copy that DPO and RLHF-PPO keep resident.

## What to look for
The two levers are **`λ`** (the weight on the odds-ratio penalty; ~0.1–0.5, with the paper's default
around 0.1) and the **learning rate** (see [[hyperparameter/learning-rate]]; ORPO trains from a base
model, so a mid-range SFT-like rate such as 8e-6–1e-5 works, higher than DPO's because there is no
reference anchoring it). Watch the balance: too large a `λ` and the SFT signal is drowned out and
fluency suffers; too small and it behaves like plain SFT with no meaningful preference shaping. The
big practical wins are memory (one model, versus DPO's two and PPO's four) and pipeline simplicity
(one stage, one dataset of preference pairs, no reward model). The tradeoff is less direct control
than a tuned two-stage pipeline, and it still needs paired preference data.

## Calculation
No new parameters and — unlike [[fine-tuning-method/dpo]] and [[fine-tuning-method/rlhf-ppo]] — **no
reference model**, so only one model is resident. Per step you run forward/backward on the chosen and
rejected sequences through the single trainable model. Trainable count equals the base method's (full
weights, or LoRA/QLoRA adapters if applied). Memory ≈ one model's training state — the lightest of
the preference methods for a given base size.

## Example
Align a 7B base model directly (no prior SFT pass) on (prompt, chosen, rejected) pairs. Train with
`λ = 0.1`, learning rate `8e-6` (cosine decay, warmup ~3%), 1–3 epochs. Only the 7B policy sits in
memory — roughly half the footprint of a DPO run at the same base size, and a quarter of RLHF-PPO —
so this fits where those would not. The single combined loss means the model learns the task format
and the human preference simultaneously; if outputs align well but read awkwardly, lower `λ` to let
the SFT term dominate, and if they read fluently but ignore the preference signal, raise it.
