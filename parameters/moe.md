---
id: parameter/moe
type: parameter
slug: moe
schema_version: 1.0
title: "Mixture-of-Experts (MoE) Parameters"
arch: moe
order: 2
color_key: moe
components:
  - { name: "num_local_experts (expert count)", rank: 1, importance: critical, badge: "Per Layer", formula: "total FFN params = num_experts × per-expert FFN", range: "8 → 256" }
  - { name: "num_experts_per_tok (top_k, active)", rank: 2, importance: critical, badge: "Per Layer", formula: "active FFN params = top_k × per-expert FFN", range: "1 → 8 (usually 2)" }
  - { name: "expert intermediate_size (FFN width)", rank: 3, importance: critical, badge: "Per Expert", formula: "per-expert = 3 × hidden × intermediate (SwiGLU)", range: "≈ 1.75–4 × hidden" }
  - { name: "router / gate", rank: 4, importance: important, badge: "Per Layer", formula: "hidden × num_experts", range: "tiny (≪ 0.1% of layer)" }
  - { name: "shared_expert (optional, always-on)", rank: 5, importance: optional, badge: "Per Layer", formula: "adds one always-active FFN to every token", range: "0 or 1" }
  - { name: "hidden_size (d_model)", rank: 6, importance: critical, badge: "Structure", formula: "scales attention and every expert", range: "2048 → 8192" }
  - { name: "num_hidden_layers (depth)", rank: 7, importance: critical, badge: "Structure", formula: "total = layers × per-layer params", range: "24 → 60" }
worked_example:
  label: "Mixtral 8x7B"
  config: { hiddenDim: 4096, numLayers: 32, attentionHeads: 32, kvHeads: 8, headDim: 128, ffnDim: 14336, vocabSize: 32000, numExperts: 8, topK: 2, activation: SwiGLU, normalization: RMSNorm, tiedEmbeddings: false }
  sources:
    - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al., 2024)", trust: 0.95 }
    - { url: "https://arxiv.org/abs/2305.13245", title: "GQA: Grouped-Query Attention", trust: 0.9 }
tags: [moe, mixture-of-experts, parameters, sparse]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al., 2024)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2101.03961", title: "Switch Transformers (Fedus et al., 2021)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1701.06538", title: "Outrageously Large Neural Networks: The Sparsely-Gated MoE Layer (Shazeer et al., 2017)", trust: 0.9 }
---

## Overview
A Mixture-of-Experts model replaces the single feed-forward block of each transformer layer with a
bank of `num_experts` independent FFNs plus a small router. Every token is embedded and attended
exactly as in a dense transformer, but the router selects only `top_k` experts to process each
token. This decouples two numbers that are identical in a dense model:

- **Total parameters** — everything stored on disk and in memory: all experts count.
- **Active parameters** — what actually multiplies against a given token: only `top_k` experts.

MoE buys the representational capacity of a huge model at the compute cost of a small one, because
the expert FFNs — which dominate parameter count — are used sparsely. The attention blocks, the
router, and (if present) a shared always-on expert are dense: they run for every token.

## Components
- **num_local_experts** sets total capacity. Expert FFNs hold the overwhelming majority of the
  weights, so total parameters scale almost linearly with the expert count.
- **num_experts_per_tok (top_k)** sets the active/compute budget. With top-2 of 8, each token pays
  for 2 experts, so the active FFN cost is 2/8 = 25% of the stored FFN weight.
- **expert intermediate_size** is the width of each expert FFN. With SwiGLU each expert has three
  matrices (gate, up, down), so per-expert = `3 × hidden × intermediate`.
- **router / gate** is a single `hidden × num_experts` linear layer producing logits over experts;
  it is numerically negligible but decides everything about load balancing.
- **shared_expert** (used by DeepSeek-MoE, Qwen-MoE) is one FFN that runs for *every* token in
  addition to the routed ones, giving a dense "floor" of capacity. Mixtral has none.

The master formulas. Let $V$ = vocab, $d$ = hidden, $L$ = layers, $E$ = num_experts, $k$ = top_k,
$d_\text{ffn}$ = expert intermediate size, and $A$ = per-layer attention params (Q,K,V,O — with GQA,
K and V shrink by `kv_heads / num_heads`). Per-expert FFN with SwiGLU is $F = 3\,d\,d_\text{ffn}$.

$$\text{Total} = V d + L\big[A + E\,F + d E + 2d\big] + d + V d$$

$$\text{Active} = V d + L\big[A + k\,F + d E + 2d\big] + d + V d$$

The only difference is $E\,F$ (all experts, stored) versus $k\,F$ (chosen experts, computed). The
router term $dE$ and the norms $2d$ are dense in both.

## Worked Example
Mixtral 8x7B (`config` above: $d=4096$, $L=32$, $E=8$, $k=2$, $d_\text{ffn}=14336$, GQA with 8 KV
heads of dim 128, $V=32000$).

Per-layer pieces:
- **Attention (GQA):** Q $4096^2 = 16.78\text{M}$, K $4096 \times 1024 = 4.19\text{M}$, V
  $4.19\text{M}$, O $16.78\text{M}$ → $A \approx 41.9\text{M}$.
- **One expert FFN (SwiGLU):** $3 \times 4096 \times 14336 \approx 176.2\text{M}$.
- **Router:** $4096 \times 8 = 32\,768 \approx 0.03\text{M}$; norms $2 \times 4096 = 8\,192$.

Stored per layer: $41.9 + 8 \times 176.2 + 0.03 + 0.008 \approx 1451.2\text{M}$. Over 32 layers
$\approx 46.44\text{B}$; add embedding $32000 \times 4096 \approx 131\text{M}$ and an untied output
head $131\text{M}$ → **≈ 46.7B total parameters**.

Active per token (top-2): $41.9 + 2 \times 176.2 + 0.03 + 0.008 \approx 394.2\text{M}$ per layer.
Over 32 layers $\approx 12.6\text{B}$; plus embedding and head → **≈ 12.9B active parameters**.

So Mixtral stores ~47B weights but does the forward-pass FLOPs of a ~13B dense model — the defining
tradeoff of MoE: pay ~47B of memory to serve at ~13B of compute.
