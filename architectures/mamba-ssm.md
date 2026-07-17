---
id: architecture-era/mamba-ssm
type: architecture-era
slug: mamba-ssm
schema_version: 1.0
title: "Mamba & Selective State-Space Models"
name: "Mamba (Selective SSM)"
era: post-transformer
era_range: "2023–2026"
year: 2023
family: llm
core_innovation: "An input-dependent (selective) state-space layer that models sequences in linear time with a fixed-size recurrent state."
description: "A subquadratic, recurrent-state alternative to attention with O(1)-per-step inference memory."
key_features: ["Selective (input-dependent) state-space parameters", "Linear-time sequence scaling", "Constant inference state (no KV-cache)", "Hardware-aware parallel scan", "Attention-free backbone"]
key_models: ["Mamba (2023)", "Mamba-2 (2024)", "hybrid SSM-attention models (2024)"]
paper_title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces"
paper_url: "https://arxiv.org/abs/2312.00752"
influenced_by: [architecture-era/rnn-lstm]
led_to: []
tags: [mamba, ssm, state-space, linear-time, subquadratic, llm]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2312.00752", title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, 2023)", trust: 0.95 }
---

## What Changed
The transformer's attention is quadratic in sequence length, and at inference it keeps a KV-cache that
grows with every generated token. Mamba revives the *recurrent* idea — a fixed-size state carried
through the sequence, in the spirit of the LSTM — but rebuilt on **state-space models (SSMs)** so it
trains efficiently in parallel. Prior SSMs were linear-time but *content-agnostic*: their dynamics were
the same regardless of the input, so they could not selectively remember or forget. Mamba's key move is
to make the SSM parameters **input-dependent (selective)** — the model chooses, per token, what to
write into and read from its state — recovering the content-based reasoning that made attention
powerful. To keep this fast on GPUs it uses a hardware-aware **parallel scan**. The payoff:
**linear-time** training in sequence length and **constant memory per step at inference** (a fixed
recurrent state instead of a growing KV-cache), which is very attractive for extremely long sequences.
It descends conceptually from RNN/LSTM recurrence and is the most prominent challenger to attention as
the sequence-modeling substrate.

## Key Ideas
- **State-space core** — a continuous-style linear recurrence maps inputs to a hidden state to outputs;
  it can be run as an efficient convolution/scan for parallel training.
- **Selectivity** — making the SSM's transition/input matrices depend on the current token lets the
  model gate information content-wise, the ability fixed-parameter SSMs lacked.
- **Linear-time, constant-state** — compute scales linearly with length and inference keeps an
  $O(1)$-size state, avoiding the transformer's growing KV-cache.
- **Recurrence, reborn** — it inherits the RNN's fixed-state efficiency but sidesteps sequential
  training via a hardware-aware parallel scan, making long-context modeling cheap.
