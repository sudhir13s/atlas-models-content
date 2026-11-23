---
id: parameter-calculator/ssm-mamba
type: parameter-calculator
slug: ssm-mamba
schema_version: 3.1
title: State-Space Model / Mamba Parameters
arch: ssm
order: 3
color_key: ssm
components:
- name: d_model (width)
  rank: 1
  importance: critical
  badge: Structure
  formula: block cost ≈ 3E × d_model² (in/out projections)
  range: 768 → 4096
- name: expand factor (E)
  rank: 2
  importance: critical
  badge: Per Block
  formula: d_inner = expand × d_model; scales every inner op
  range: 2 (default)
- name: num_hidden_layers (depth)
  rank: 3
  importance: critical
  badge: Structure
  formula: total = layers × per-block params
  range: 24 → 64
- name: in_proj (input + gate)
  rank: 4
  importance: critical
  badge: Per Block
  formula: d_model × 2·d_inner (SSM path + gating path)
  range: 2E·d² (dominant)
- name: out_proj
  rank: 5
  importance: critical
  badge: Per Block
  formula: d_inner × d_model
  range: E·d² (dominant)
- name: token embedding table
  rank: 6
  importance: important
  badge: Embedding
  formula: vocab_size × d_model (tied → doubles as head)
  range: V = 50k+
- name: A parameter (A_log state matrix)
  rank: 7
  importance: important
  badge: Per Block
  formula: d_inner × d_state (only stored SSM tensor besides D)
  range: d_inner·N
- name: d_state (N, SSM memory)
  rank: 8
  importance: important
  badge: Per Block
  formula: sizes A, and B/C inside x_proj; mostly runtime state
  range: 16 → 128
- name: x_proj (selective Δ, B, C)
  rank: 9
  importance: important
  badge: Per Block
  formula: d_inner × (dt_rank + 2·d_state)
  range: small
- name: dt_proj + dt_rank (Δ projection)
  rank: 10
  importance: optional
  badge: Per Block
  formula: dt_rank × d_inner; dt_rank ≈ ceil(d_model/16)
  range: auto
- name: conv1d (short depthwise causal conv)
  rank: 11
  importance: optional
  badge: Per Block
  formula: d_inner × d_conv (+ d_inner bias)
  range: d_conv 3 → 4
- name: D (skip connection)
  rank: 12
  importance: optional
  badge: Per Block
  formula: d_inner (per-channel skip scalar)
  range: d_inner
- name: gating branch (z, SiLU)
  rank: 13
  importance: architectural
  badge: Per Block
  formula: 0 extra — it is the second half of in_proj
  range: n/a
- name: RMSNorm (per block + final)
  rank: 14
  importance: optional
  badge: Per Block
  formula: ≈ (L+1)·d_model
  range: tiny
worked_example:
  label: Mamba-130M
  config:
    dModel: 768
    num_layers: 24
    expand: 2
    dInner: 1536
    dState: 16
    dtRank: 48
    dConv: 4
    vocabulary_size: 50280
    normalization: RMSNorm
    tiedEmbeddings: true
  sources:
  - url: https://arxiv.org/abs/2312.00752
    title: 'Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, 2023)'
    trust: 0.95
tags:
- ssm
- mamba
- state-space
- parameters
updated: '2026-07-17'
sources:
- url: https://arxiv.org/abs/2312.00752
  title: 'Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, 2023)'
  trust: 0.95
- url: https://arxiv.org/abs/2111.00396
  title: Efficiently Modeling Long Sequences with Structured State Spaces (S4, Gu et al., 2021)
  trust: 0.9
---

## Overview

A state-space model replaces attention with a linear recurrence: it carries a hidden state of size
`d_state` and updates it token by token, giving O(sequence length) time and O(1) memory per step
instead of attention's quadratic cost. Mamba makes the recurrence **selective** — the state-update
gates (Δ, B, C) are functions of the input — which is what closes the quality gap with transformers.

Because there is no attention and no large feed-forward expansion, a Mamba block's parameters are
dominated by two dense projections: an **input projection** that maps `d_model → 2 × d_inner` (one
path for the SSM, one gating path) and an **output projection** that maps `d_inner → d_model`. With
the default `expand = 2`, these two projections cost about `6 × d_model²` per block — the SSM-specific
tensors (Δ, A, B, C, D, the short conv) are comparatively small.

## Components

- **d_model** is the model width and the single biggest lever: block cost grows with `d_model²`.
- **expand (E)** sets `d_inner = E × d_model` (the SSM works in this widened space). The dominant
  projections cost `2E·d_model²` (in) + `E·d_model²` (out) = `3E·d_model²`; with E=2 that is `6·d_model²`.
- **num_hidden_layers** multiplies the per-block cost; Mamba typically uses ~2× the layer count of a
  comparable transformer since each block is cheaper.
- **d_state (N)** is the size of the recurrent memory. It appears in `A_log` (`d_inner × N`) and in
  the `B`/`C` projections, but N is small (16 by default), so its parameter contribution is minor —
  its real cost is runtime state, not stored weights.
- **dt_rank** is the low rank used to produce the selective timestep Δ; it sizes `x_proj`
  (`d_inner → dt_rank + 2N`) and `dt_proj` (`dt_rank → d_inner`).
- **d_conv** is a tiny depthwise causal convolution (`d_inner × d_conv`) applied before the SSM to
  mix adjacent tokens.

The master formula per Mamba block (let $d$ = d_model, $E$ = expand, $d_i = E d$, $N$ = d_state,
$r$ = dt_rank, $c$ = d_conv):

$$P_\text{block} = \underbrace{2 d\,d_i}_{\text{in\_proj}} + \underbrace{d_i c}_{\text{conv}} + \underbrace{d_i(r + 2N)}_{\text{x\_proj}} + \underbrace{r\,d_i}_{\text{dt\_proj}} + \underbrace{d_i N}_{A} + \underbrace{d_i}_{D} + \underbrace{d_i d}_{\text{out\_proj}}$$

The first and last terms ($2d\,d_i + d_i d = 3E d^2$) dominate. Full model:
$\text{Total} = V d + L\,P_\text{block} + \text{norms} + (V d\ \text{if untied})$.

## Worked Example

Mamba-130M (`config` above: $d=768$, $E=2 \Rightarrow d_i=1536$, $N=16$, $r=48$, $c=4$, $L=24$,
$V=50280$, tied embeddings).

Per-block pieces:
- **in_proj:** $768 \times 3072 = 2.359\text{M}$
- **out_proj:** $1536 \times 768 = 1.180\text{M}$
- **x_proj:** $1536 \times (48 + 32) = 1536 \times 80 = 0.123\text{M}$
- **dt_proj:** $48 \times 1536 = 0.074\text{M}$
- **A_log:** $1536 \times 16 = 0.025\text{M}$; **conv1d:** $1536 \times 4 = 0.006\text{M}$;
  **D** $1536$; RMSNorm $768$.

Per block $\approx 3.77\text{M}$. Over 24 layers $\approx 90.5\text{M}$. Add the embedding table
$50280 \times 768 \approx 38.6\text{M}$ (tied, so it doubles as the output head) → **≈ 130M
parameters**, matching the published Mamba-130M. Note the two projections ($2.359 + 1.180 = 3.54\text{M}$)
are ~94% of every block — exactly the `6·d_model²` heuristic ($6 \times 768^2 \approx 3.54\text{M}$).
