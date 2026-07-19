---
id: parameter/ssm-state-dim
type: parameter
slug: ssm-state-dim
schema_version: 1.0
title: "State Dimension (d_state, N)"
name: d_state
priority: 3
importance: important
applies_to: [ssm]
range: "16 → 128"
controls: "The size of the recurrent hidden state the SSM carries per channel. It sets the model's memory capacity — but its parameter cost is minor; its main cost is runtime state."
formula: "sizes A (d_inner × d_state) and B/C inside x_proj; a small share of block params"
if_increased: "More memory to summarize long-range history, improving long-context modeling — but mostly larger runtime state (activations), only a small parameter increase."
if_decreased: "Less recurrent memory and cheaper runtime state; below a point the model cannot retain enough history to model long dependencies."
tags: [ssm, mamba, state-space, state, memory, parameters]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/2312.00752", title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2111.00396", title: "Efficiently Modeling Long Sequences with Structured State Spaces (S4, Gu et al., 2021)", trust: 0.9 }
---

## Definition
`d_state` (`N`) is the size of the **recurrent hidden state** the state-space model carries — the
memory it updates token by token to summarize everything seen so far. It is the SSM analogue of an
RNN's hidden size, but with a crucial difference: it affects capacity far more than parameter count.

Its main cost is **runtime state**, not stored weights. `N` appears in the `A` matrix (`d_inner × N`)
and inside the `B`/`C` projections, but those are a small fraction of a block dominated by the dense
`d_model²` projections.

## What to look for
`d_state` trades long-range memory against activation memory, not really against model size:

- **16 (Mamba default):** surprisingly small, yet effective because the recurrence is *selective*
  (Δ, B, C depend on the input).
- **64–128:** more memory for long-context tasks.

The knob to watch here is inference/training **memory**, not parameter count — a larger `N` means a
larger state tensor to hold per step, even though the weight increase is minor.

## Calculation
`N` sizes only the SSM-specific tensors, which are small relative to the block:

$$P_A = d_\text{inner} \times N, \qquad x\_proj\ \text{output} = \text{dt\_rank} + 2N$$

For Mamba-130M (`d_inner = 1536`), `A_log` is $1536 \times 16 = 24\,576$ — under 1% of the block's
~3.77M. Doubling `N` roughly doubles this tiny term while leaving the dominant projections untouched.

## Example
Mamba-130M with `N = 16`: `A_log` is $1536 \times 16 \approx 0.025\text{M}$ and the `x_proj` output is
`48 + 2·16 = 80` wide — negligible next to the ~3.54M projections. Raising `N` to 64 grows `A_log` to
~0.098M and widens `x_proj` slightly; the block barely changes size, but the recurrent state it must
hold at runtime grows 4×. `d_state` buys memory capacity, not parameters.
