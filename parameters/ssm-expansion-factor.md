---
id: parameter/ssm-expansion-factor
type: parameter
slug: ssm-expansion-factor
schema_version: 3.1
title: Expansion Factor (expand)
name: expand
priority: 2
importance: critical
applies_to:
- ssm
range: 2 (default)
controls: 'How much wider the SSM''s inner working space is than the model width: d_inner = expand × d_model. It scales the two dominant projections in every block.'
formula: dominant projections cost 3·expand·d_model² per block (in_proj 2E·d² + out_proj E·d²)
if_increased: A wider inner space gives each block more capacity, but the two big projections cost 3·E·d_model² — so parameters scale linearly with the expansion factor.
if_decreased: A narrower inner space shrinks every block proportionally, cheaper but with less per-block capacity.
tags:
- ssm
- mamba
- state-space
- expand
- width
- parameters
updated: '2026-07-19'
sources:
- url: https://arxiv.org/abs/2312.00752
  title: 'Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, 2023)'
  trust: 0.95
---

## Definition

`expand` (`E`) sets how much wider the SSM's internal space is than the model width: `d_inner = expand
× d_model`. A Mamba block projects the residual stream up into this widened `d_inner` space, runs the
selective state-space recurrence there, then projects back down.

It scales the two projections that dominate a block's parameter count — the input projection
(`d_model → 2·d_inner`) and the output projection (`d_inner → d_model`) — so `E` is a near-linear lever
on block size.

## What to look for

`expand = 2` is the Mamba default and is rarely changed, so the practical width lever is usually
`d_model`. When it is tuned:

- Larger `E` widens the inner recurrence — more capacity per block, more parameters.
- The SSM-specific tensors (Δ, A, B, C, D, the short conv) also live in `d_inner`, but they are small;
  the two dense projections are what `E` really scales.

Think of `E` as a multiplier that sits on top of `d_model` to set the true working width of the block.

## Calculation

With `d_inner = E·d_model`, the two dominant projections are:

$$\underbrace{2\,d_\text{model}\,d_\text{inner}}_{\text{in\_proj}} + \underbrace{d_\text{inner}\,d_\text{model}}_{\text{out\_proj}} = 3E\,d_\text{model}^2$$

At the default `E = 2` this is `6·d_model²` per block — the ~94% of the block that matters. Parameters
scale **linearly** in `E` and **quadratically** in `d_model`.

## Example

Mamba-130M (`d_model = 768`, `E = 2 → d_inner = 1536`): in_proj $768 \times 3072 = 2.36\text{M}$,
out_proj $1536 \times 768 = 1.18\text{M}$, summing to $3.54\text{M}$ — exactly $6 \times 768^2$. Raise
`E` to 4 and those projections double to ~7.1M per block, roughly doubling the whole model; the SSM
tensors barely move because they are already a small share.
