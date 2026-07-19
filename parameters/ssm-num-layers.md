---
id: parameter/ssm-num-layers
type: parameter
slug: ssm-num-layers
schema_version: 1.0
title: "Number of Layers (Depth)"
name: n_layer
priority: 1
importance: critical
applies_to: [ssm]
range: "24 → 64"
controls: "How many Mamba/SSM blocks are stacked — the model's depth. Every per-block cost is multiplied by this count."
formula: "total = n_layer × per-block params (+ embedding); depth multiplies the whole per-block term"
if_increased: "More representational depth and capacity, but linearly more parameters and compute — SSMs typically run deeper than transformers since each block is cheaper."
if_decreased: "A smaller, faster model with less compositional depth; very shallow SSMs lose the sequential reasoning that depth provides."
tags: [ssm, mamba, state-space, depth, structure, parameters]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/2312.00752", title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2111.00396", title: "Efficiently Modeling Long Sequences with Structured State Spaces (S4, Gu et al., 2021)", trust: 0.9 }
---

## Definition
`n_layer` is the number of stacked **Mamba/SSM blocks** — the model's depth (`L`). Each block replaces
a transformer's attention + feed-forward with a selective state-space recurrence plus its input/output
projections, wrapped in a residual connection and normalization.

Depth is one of the two dominant size levers (the other is the width `d_model`), because every
per-block cost is multiplied by `L`.

## What to look for
Because a Mamba block is cheaper than a transformer block (no quadratic attention, no large FFN
expansion), SSMs tend to be **deeper** at a given parameter budget:

- Mamba-130M: 24 layers; Mamba-2.8B: 64 layers.
- Rule of thumb: roughly **~2× the layer count** of a comparable transformer.

Depth and width should still grow together — a very deep, very narrow SSM optimizes poorly, just as in
any deep network.

## Calculation
Depth multiplies the entire per-block term:

$$\text{Total} = V d + L \cdot P_\text{block} + \text{norms} \;(+\, V d\ \text{if untied})$$

where `P_block ≈ 3E·d_model²` is dominated by the input and output projections. Doubling `L` roughly
doubles everything except the (fixed) embedding table.

## Example
Mamba-130M: `L = 24`, `d_model = 768`, `expand = 2`. Each block is ~3.77M parameters, so the stack is
$24 \times 3.77 \approx 90.5\text{M}$; adding the ~38.6M tied embedding gives ~130M. Halve the depth to
12 layers and the stack drops to ~45M — the embedding is unchanged, so the model falls to roughly 84M,
depth carrying the bulk of the change.
