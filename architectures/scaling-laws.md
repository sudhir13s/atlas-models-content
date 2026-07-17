---
id: architecture-era/scaling-laws
type: architecture-era
slug: scaling-laws
schema_version: 1.0
title: "Neural Scaling Laws"
name: "Scaling Laws"
era: scaling
era_range: "2020–2022"
year: 2020
family: llm
core_innovation: "Loss falls as a predictable power law in parameters, data, and compute — enabling compute-optimal model sizing."
description: "The empirical power laws (Kaplan; Chinchilla) that turned model scaling from guesswork into budgeting."
key_features: ["Power-law loss vs. compute/params/data", "Predictable extrapolation", "Compute-optimal frontier", "~20 tokens per parameter (Chinchilla)"]
key_models: ["Kaplan et al. study (2020)", "Chinchilla 70B (2022)"]
paper_title: "Scaling Laws for Neural Language Models"
paper_url: "https://arxiv.org/abs/2001.08361"
influenced_by: [architecture-era/gpt]
led_to: []
tags: [scaling-laws, compute-optimal, chinchilla, llm]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2001.08361", title: "Scaling Laws for Neural Language Models (Kaplan et al., 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2203.15556", title: "Training Compute-Optimal Large Language Models (Chinchilla; Hoffmann et al., 2022)", trust: 0.95 }
---

## What Changed
Once GPT showed that bigger models were better, the open question was *how much* better, and where to
spend a fixed compute budget. Scaling laws answered it empirically. Kaplan et al. (2020) found that
test loss falls as a smooth **power law** in model size, dataset size, and compute over many orders of
magnitude — meaning you can train small models and *extrapolate* the loss of a much larger one before
building it. Hoffmann et al. (2022, "Chinchilla") then corrected the recipe: earlier large models
(including GPT-3) were badly *undertrained* on too little data. For a compute budget, parameters and
training tokens should scale together — roughly **20 tokens per parameter** — so a smaller model
trained on far more data (Chinchilla-70B) beat much larger ones. This reframed scaling from folklore
into a budgeting discipline: given FLOPs, solve for the optimal (model size, token count) pair. Every
serious pretraining run since is planned around these curves.

## Key Ideas
- **Power-law loss** — $L$ decreases predictably as a power of parameters $N$, data $D$, and compute
  $C$; the exponents are stable enough to extrapolate.
- **Predictable extrapolation** — fit the curve on cheap small runs, forecast the loss of an expensive
  large run, and de-risk the budget before committing.
- **Compute-optimal frontier** — for fixed compute there is a best (size, tokens) trade-off; too big a
  model on too few tokens wastes compute.
- **Chinchilla correction** — ~20 tokens/parameter; prior frontier models were oversized and
  data-starved, so a smaller-but-longer-trained model won at equal compute.
