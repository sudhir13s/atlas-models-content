---
id: parameter/ssm-conv-width
type: parameter
slug: ssm-conv-width
schema_version: 1.0
title: "Convolution Width (d_conv)"
name: d_conv
priority: 4
importance: optional
applies_to: [ssm]
range: "3 → 4"
controls: "The kernel length of the short depthwise causal convolution applied before the SSM, which mixes each channel with its immediate neighbors in time."
formula: "params = d_inner × d_conv (+ d_inner bias); a tiny share of the block"
if_increased: "A slightly wider local window before the recurrence, capturing a bit more short-range context — but the parameter cost is negligible (linear in d_conv)."
if_decreased: "An even smaller local mixing window; below width 2 there is no neighbor mixing at all, leaving all temporal modeling to the SSM recurrence."
tags: [ssm, mamba, state-space, convolution, parameters]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/2312.00752", title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, 2023)", trust: 0.95 }
---

## Definition
`d_conv` is the kernel length of the **short depthwise causal convolution** each Mamba block applies to
its inner sequence just before the selective state-space recurrence. It mixes every channel with its
few immediate predecessors in time, giving the block cheap local context.

Being **depthwise** (one filter per channel) and short, it is one of the smallest parameter
contributors in the block — a minor architectural detail, not a size lever.

## What to look for
`d_conv` is almost always **4** in Mamba and rarely tuned. What matters conceptually:

- It is **causal** (only looks backward), preserving the left-to-right structure needed for
  autoregressive modeling.
- It provides short-range mixing that complements the SSM's long-range recurrence — a division of labor
  between local (conv) and global (state) context.

Treat it as a fixed structural choice; the real width levers are `d_model` and `expand`.

## Calculation
A depthwise conv of width `d_conv` over `d_inner` channels has one length-`d_conv` filter per channel:

$$P_\text{conv1d} = d_\text{inner} \times d_\text{conv} \;(+\, d_\text{inner}\ \text{bias})$$

There is no `d_inner²` term because it is depthwise, not full — which is exactly why it stays tiny.

## Example
Mamba-130M (`d_inner = 1536`, `d_conv = 4`): $1536 \times 4 = 6\,144$ weights (plus a 1536 bias) —
about 0.16% of the block's ~3.77M parameters. Even widening `d_conv` to 8 only reaches ~12K, still
negligible. The short conv earns its place through local mixing, not parameter budget.
