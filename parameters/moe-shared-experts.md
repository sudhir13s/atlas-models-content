---
id: parameter/moe-shared-experts
type: parameter
slug: moe-shared-experts
schema_version: 1.0
title: "Shared Experts (n_shared_experts)"
name: n_shared_experts
priority: 4
importance: important
applies_to: [moe]
range: "0 (Mixtral) → 1–2 (DeepSeek-MoE, Qwen-MoE)"
controls: "How many always-on expert FFNs run for every token, in addition to the routed ones. They provide a dense capacity 'floor' shared across all tokens."
formula: "adds one always-active FFN (3 × hidden × intermediate) per shared expert to every token — dense, counted in both total and active"
if_increased: "A stronger shared knowledge base captures common patterns so routed experts can specialize — but each shared expert is dense, adding to both total and active cost."
if_decreased: "Fewer or no shared experts (Mixtral's choice) means all capacity is routed; simpler, but routed experts must also cover the common cases."
tags: [moe, mixture-of-experts, shared-expert, routing, parameters, sparse]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/2401.06066", title: "DeepSeekMoE: Towards Ultimate Expert Specialization (Dai et al., 2024)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al., 2024)", trust: 0.9 }
---

## Definition
A **shared expert** is a feed-forward network that runs for **every** token, on top of the `top_k`
routed experts. Where routed experts are conditionally activated by the router, shared experts are
**always on** — a dense capacity "floor" that all tokens pass through.

The idea (DeepSeek-MoE, Qwen-MoE) is a division of labor: shared experts absorb **common** knowledge so
the routed experts are free to **specialize**, rather than every routed expert re-learning the basics.

## What to look for
Shared experts change the dense/sparse balance of a MoE:

- **0 shared (Mixtral):** all FFN capacity is routed — pure sparse mixture.
- **1–2 shared (DeepSeek-MoE, Qwen-MoE):** a small always-on base plus many fine-grained routed experts.

Because a shared expert is **dense** (runs for all tokens), it counts in **both** total and active
parameters — unlike routed experts, which only count fully in the total. It is effectively a partial
return to a dense FFN alongside the sparse bank.

## Calculation
Each shared expert adds a full FFN that is active for every token:

$$P_\text{shared} = n_\text{shared} \cdot 3\,d\,d_\text{ffn}\quad(\text{added to both total and active})$$

So the active FFN per layer becomes `(k + n_shared)·F` instead of `k·F`, while total becomes
`(E + n_shared)·F`. Shared experts raise the active floor, which is why they are usually few and
often narrower than routed experts.

## Example
Take a DeepSeek-style layer with `d = 4096`, routed experts at `d_ffn = 1408` (fine-grained,
$F_\text{routed} = 3 \times 4096 \times 1408 \approx 17.3\text{M}$) and 1 shared expert at the same
width. Every token runs `top_k` routed experts **plus** the shared one, so the shared expert adds
~17.3M to the active budget of that layer for all tokens — a dense contribution the router cannot
switch off, in exchange for freeing routed experts to specialize.
