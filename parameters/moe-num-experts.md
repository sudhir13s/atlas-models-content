---
id: parameter/moe-num-experts
type: parameter
slug: moe-num-experts
schema_version: 1.0
title: "Number of Experts (num_local_experts)"
name: num_local_experts
priority: 1
importance: critical
applies_to: [moe]
range: "8 → 256"
controls: "How many independent feed-forward experts sit in each MoE layer. It sets the model's total (stored) capacity, since expert FFNs hold the overwhelming majority of parameters."
formula: "total FFN params per layer = num_experts × per-expert FFN (3 × hidden × intermediate)"
if_increased: "More total capacity and specialization, with total parameters scaling almost linearly — but active compute is unchanged (only top_k experts run per token)."
if_decreased: "A smaller, lighter model with less total capacity; fewer experts means less room for specialization across the token distribution."
tags: [moe, mixture-of-experts, experts, capacity, parameters, sparse]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al., 2024)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2101.03961", title: "Switch Transformers (Fedus et al., 2021)", trust: 0.9 }
---

## Definition
`num_local_experts` (`E`) is how many independent feed-forward networks (FFNs) — the "experts" — each
MoE layer holds in place of a dense transformer's single FFN. A small router picks which experts
process each token, but **all** experts are stored in memory.

Because the expert FFNs dominate a MoE model's parameter count, `E` is the primary lever on **total
(stored)** size — total parameters scale almost linearly with the expert count.

## What to look for
Expert count sets total capacity, decoupled from compute:

- **8 (Mixtral):** the well-known 8-expert design.
- **64–256 (DeepSeek-MoE, Switch):** many fine-grained experts for higher capacity.

The crucial distinction is **total vs active** parameters: `E` grows the stored model, but each token
still runs only `top_k` experts, so the *compute* per token is set by `top_k`, not `E`. This is the
whole point of MoE — capacity without proportional compute.

## Calculation
Each expert is a SwiGLU FFN with three matrices (gate, up, down): `F = 3·d·d_ffn`. Per layer, all
experts are stored:

$$\text{stored FFN per layer} = E \cdot F = E \cdot 3\,d\,d_\text{ffn}$$

versus the **active** cost `k·F` (only `top_k` experts run). Total parameters therefore scale linearly
in `E`, while active parameters do not depend on `E` at all.

## Example
Mixtral 8x7B (`d = 4096`, `d_ffn = 14336`): one expert FFN is $3 \times 4096 \times 14336 \approx
176.2\text{M}$. With `E = 8`, the stored FFN per layer is $8 \times 176.2 \approx 1.41\text{B}$; over
32 layers plus attention and embeddings the model is ~46.7B total. Double `E` to 16 and total climbs
toward ~90B — yet with top-2 routing the active (~13B) compute is unchanged.
