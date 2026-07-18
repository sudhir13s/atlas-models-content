---
id: parameter-calculator/hybrid
type: parameter-calculator
slug: hybrid
schema_version: 1.0
title: "Hybrid SSM / Attention / MoE Parameters"
arch: hybrid
order: 9
color_key: hybrid
components:
  - { name: "layer-type ratio (attention : SSM : MoE)", rank: 1, importance: critical, badge: "Structure", formula: "determines how the parameter budget splits across layer kinds", range: "e.g. 1:7 attn:Mamba" }
  - { name: "num_hidden_layers (total depth)", rank: 2, importance: critical, badge: "Structure", formula: "total = Σ per-layer params over mixed layer types", range: "32 → 72" }
  - { name: "hidden_size (d_model)", rank: 3, importance: critical, badge: "Structure", formula: "shared width across attention, SSM and MoE layers", range: "2048 → 8192" }
  - { name: "MoE FFN layers (experts)", rank: 4, importance: critical, badge: "Per MoE Layer", formula: "num_experts × (3 × d × d_ffn); stored, not all active", range: "8 → 16 experts" }
  - { name: "num_experts / top_k (total vs active)", rank: 5, importance: critical, badge: "Per MoE Layer", formula: "active = top_k × per-expert FFN; total = num_experts × per-expert", range: "top-2 of 16" }
  - { name: "Mamba (SSM) layers", rank: 6, importance: critical, badge: "Per SSM Layer", formula: "≈ 3E·d² (in/out proj) + small SSM tensors", range: "majority of layers" }
  - { name: "attention layers (interleaved)", rank: 7, importance: critical, badge: "Per Attn Layer", formula: "Q,O ≈ d² each; K,V shrunk by GQA", range: "the minority (1 in N)" }
  - { name: "dense FFN layers (non-MoE positions)", rank: 8, importance: important, badge: "Per Layer", formula: "3 × d × d_ffn where MoE is not applied", range: "layers between MoE slots" }
  - { name: "token embedding table", rank: 9, importance: important, badge: "Embedding", formula: "vocab_size × d_model", range: "V·d" }
  - { name: "output / unembedding head", rank: 10, importance: important, badge: "Head", formula: "vocab_size × d_model (0 if tied)", range: "0 or V·d" }
  - { name: "router / gate (per MoE layer)", rank: 11, importance: important, badge: "Per MoE Layer", formula: "d_model × num_experts logits", range: "tiny" }
  - { name: "MoE placement interval (every-N layers)", rank: 12, importance: architectural, badge: "Structure", formula: "MoE replaces the MLP every e layers; sets expert count", range: "every 1–2 layers" }
  - { name: "RMSNorm (per layer + final)", rank: 13, importance: optional, badge: "Per Layer", formula: "≈ (2L+1)·d_model", range: "tiny" }
worked_example:
  label: "Jamba-v0.1 (52B total / 12B active, approximate split)"
  config: { hiddenDim: 4096, blockLayers: 8, attentionToMambaRatio: "1:7", moeEveryNLayers: 2, numExperts: 16, topK: 2, ffnDim: 14336, totalParams: "52B", activeParams: "12B" }
  sources:
    - { url: "https://arxiv.org/abs/2403.19887", title: "Jamba: A Hybrid Transformer-Mamba Language Model (Lieber et al., 2024)", trust: 0.95 }
tags: [hybrid, ssm, mamba, transformer, moe, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2403.19887", title: "Jamba: A Hybrid Transformer-Mamba Language Model (Lieber et al., 2024)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2408.12570", title: "Jamba-1.5: Hybrid Transformer-Mamba Models at Scale (Team Jamba, 2024)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2312.00752", title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al., 2024)", trust: 0.9 }
---

## Overview
A hybrid model interleaves *different kinds of layer* in one stack instead of repeating a single
block. The archetype is **Jamba**, which alternates **Mamba (SSM) layers**, **Transformer
attention layers**, and **Mixture-of-Experts FFN layers** — combining Mamba's linear-time
memory efficiency, attention's exact recall, and MoE's cheap capacity. The consequence for
parameter counting is that there is no single "per-layer" cost: the total is a *weighted sum
over layer types*, and — because of the MoE layers — it splits into **total** (everything
stored) versus **active** (what a token actually touches), exactly as in a pure MoE.

The design intent is to spend parameters where they pay off and compute where it is cheap. Most
layers are Mamba (cheap sequence mixing, small KV footprint); a few are attention (for precise
long-range lookups); and the FFN positions are sparsified into experts so capacity can grow
without a matching rise in per-token FLOPs. Reading a hybrid's parameter count means bucketing
its layers by type first, then summing each bucket.

## Components
- **Layer-type ratio** is the defining knob: Jamba uses a **1:7 attention-to-Mamba** ratio, so
  most layers are the (relatively small) Mamba blocks and only one in eight is attention.
- **Mamba layers** cost ~`3E·d²` (dominated by their in/out projections; see the SSM page); at
  `d = 4096`, `E = 2` that is ~`100M` each before the small SSM tensors.
- **Attention layers** cost `Q,O ≈ d²` plus GQA-shrunk `K,V` — Jamba deliberately keeps few of
  them, which is what makes its 256K-token KV cache small.
- **MoE FFN layers** replace the MLP every `e` layers (`e = 2` in Jamba) with `num_experts`
  expert FFNs (16) of which only `top_k` (2) fire per token. This is where total and active
  diverge: all 16 experts are stored, only 2 run.
- **Dense FFN layers** occupy the non-MoE positions; **router**, **embedding/head**, and
  **RMSNorm** are all dense and shared across layer types.

The master formula. Let the model have `L_ssm` Mamba layers, `L_attn` attention layers,
`L_moe` MoE-FFN layers and `L_ffn` dense-FFN layers (with `L_ssm + L_attn + L_moe + L_ffn = L`),
per-expert FFN `F = 3·d·d_ffn`, `E` experts, top-`k`:

$$\text{Total} = V d + L_\text{ssm}P_\text{mamba} + L_\text{attn}P_\text{attn} + L_\text{ffn}F + L_\text{moe}(E F + dE) + \text{norms} + (V d\ \text{if untied})$$

$$\text{Active} = V d + L_\text{ssm}P_\text{mamba} + L_\text{attn}P_\text{attn} + L_\text{ffn}F + L_\text{moe}(k F + dE) + \text{norms} + (V d\ \text{if untied})$$

Only the MoE term changes between the two (`E F` stored versus `k F` computed); every other layer
type is dense in both.

## Worked Example
Jamba-v0.1 (`config` above): `d_model = 4096`, blocks of **8 layers** at a **1:7 attention:Mamba
ratio**, an MoE FFN in place of the MLP **every 2 layers**, **16 experts**, **top-2** active,
`d_ffn = 14336`. Per-layer-type costs:

- **One Mamba layer:** in/out projections $3E d^2 = 6 \times 4096^2 \approx 100.7\text{M}$, plus
  small SSM tensors (conv1d, `x_proj`, `dt_proj`, `A`, `D`) → **≈ 101M**.
- **One attention layer (GQA):** $Q,O \approx 2 \times 4096^2 = 33.6\text{M}$ plus shrunk `K,V`
  → **≈ 40M**.
- **One MoE FFN layer:** per-expert $F = 3 \times 4096 \times 14336 \approx 176\text{M}$; stored
  $16 \times 176\text{M} \approx 2.82\text{B}$; active (top-2) $2 \times 176\text{M} \approx
  352\text{M}$; router $4096 \times 16 \approx 0.07\text{M}$.

The MoE layers dwarf everything else in *stored* weight — this is why the published totals land
at **≈ 52B parameters stored but only ≈ 12B active per token**: the model carries the memory of a
52B network while doing the forward-pass FLOPs of a ~12B one. The Mamba-heavy layer mix keeps
those active FLOPs cheap and the KV cache tiny (only the few attention layers hold one), which is
the whole reason to build a hybrid rather than a pure Transformer-MoE. The per-layer figures are
exact for the given `d`/`d_ffn`; the 52B/12B totals are the published anchors, since the exact
layer counts per released checkpoint set the final sum.
