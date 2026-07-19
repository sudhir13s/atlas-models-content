---
id: parameter/moe-top-k
type: parameter
slug: moe-top-k
schema_version: 1.0
title: "Top-k (num_experts_per_tok)"
name: num_experts_per_tok
priority: 3
importance: critical
applies_to: [moe]
range: "1 → 8 (usually 2)"
controls: "How many experts the router activates for each token. It sets the ACTIVE (compute) parameter count — not the total stored size."
formula: "active FFN params = top_k × per-expert FFN; total stored is unaffected by top_k"
if_increased: "Each token is processed by more experts → more compute and often better quality, but active FLOPs scale linearly with top_k while stored size stays fixed."
if_decreased: "Fewer active experts per token → cheaper inference (top-1 is the extreme), at some quality cost; total parameters are unchanged either way."
tags: [moe, mixture-of-experts, routing, top-k, compute, sparse]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al., 2024)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2101.03961", title: "Switch Transformers (Fedus et al., 2021)", trust: 0.9 }
---

## Definition
`num_experts_per_tok` (`top_k`) is how many experts the router selects to process **each token**. Of
the `num_experts` available, only the `top_k` highest-scoring are run and their outputs combined — the
rest sit idle for that token.

It is the lever that sets **active** parameters (what actually multiplies against a token, i.e.
compute), as distinct from **total** parameters (everything stored). This decoupling is what makes a
MoE serve like a small model while holding the weights of a large one.

## What to look for
`top_k` is the compute dial, and it is kept small:

- **top-1 (Switch Transformer):** the cheapest — one expert per token.
- **top-2 (Mixtral, most MoEs):** the common sweet spot — two experts blended per token.

Total stored size is **independent of `top_k`** — raising it does not add parameters, only FLOPs and
inference latency. It also interacts with **load balancing**: the router must spread tokens across
experts so none is starved or overloaded (enforced by an auxiliary loss).

## Calculation
`top_k` multiplies the *active* expert cost, leaving the *stored* cost (set by `E`) untouched:

$$\text{Active FFN per layer} = k \cdot F, \qquad \text{Stored FFN per layer} = E \cdot F, \quad F = 3\,d\,d_\text{ffn}$$

So the active fraction of the expert bank is `k / E`. With top-2 of 8, a token pays for `2/8 = 25%` of
the stored FFN weights.

## Example
Mixtral 8x7B (`E = 8`, `k = 2`, one expert ~176.2M): active FFN per layer is $2 \times 176.2 =
352.4\text{M}$, versus $8 \times 176.2 = 1.41\text{B}$ stored. Across the model this is ~13B active vs
~47B total. Switch to top-1 and active FFN halves to ~176M/layer (~7B active) with **zero** change to
the 47B stored — `top_k` moves compute, not size.
