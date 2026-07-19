---
id: parameter/moe-expert-ffn-size
type: parameter
slug: moe-expert-ffn-size
schema_version: 1.0
title: "Expert FFN Size (moe_intermediate_size)"
name: moe_intermediate_size
priority: 2
importance: critical
applies_to: [moe]
range: "≈ 1.75 × hidden (fine-grained) → 4 × hidden"
controls: "The intermediate width of each individual expert's feed-forward network. It sets how large every expert is, and every expert carries this cost."
formula: "per-expert FFN = 3 × hidden_size × intermediate_size (SwiGLU: gate, up, down)"
if_increased: "Each expert becomes more capable, but the cost is multiplied by the number of experts — a small per-expert increase inflates the whole layer sharply."
if_decreased: "Smaller experts shrink both total and active parameters; fine-grained MoE deliberately uses narrow experts and many of them."
tags: [moe, mixture-of-experts, ffn, expert, width, parameters, sparse]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al., 2024)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1701.06538", title: "Outrageously Large Neural Networks: The Sparsely-Gated MoE Layer (Shazeer et al., 2017)", trust: 0.9 }
---

## Definition
The expert FFN size (`moe_intermediate_size`, `d_ffn`) is the **intermediate width** of each individual
expert's feed-forward network — the dimension it expands the hidden state to before projecting back.
It sets how large a single expert is.

Because this per-expert cost is multiplied by the expert count, it is a second dominant lever on MoE
size: a modest change to expert width scales through **every** expert in **every** layer.

## What to look for
Expert width, combined with expert count, defines the MoE design philosophy:

- **Wide experts, few of them (Mixtral):** `d_ffn ≈ 3.5×` hidden, 8 experts — each expert is a full
  transformer FFN.
- **Narrow experts, many of them (fine-grained, DeepSeek-MoE):** `d_ffn` well below hidden, dozens of
  experts — more specialization per parameter.

Watch that expert width also sets **active** cost: since `top_k` experts run per token, wider experts
raise the compute per token, not just the stored size.

## Calculation
With SwiGLU, each expert has three matrices (gate, up, down):

$$F = 3 \cdot d \cdot d_\text{ffn}$$

Per layer, stored `= E·F` and active `= k·F`. The intermediate size enters **both** linearly, so
widening experts inflates total and active parameters together — unlike expert *count*, which grows
only the total.

## Example
Mixtral 8x7B (`d = 4096`, `d_ffn = 14336`): one expert is $3 \times 4096 \times 14336 \approx
176.2\text{M}$. Trim `d_ffn` to 7168 (2× hidden) and each expert halves to ~88M; across 8 experts and
32 layers that removes ~22B from the stored model and halves the FFN part of the ~13B active budget.
Expert width scales the whole expert bank, stored and active alike.
