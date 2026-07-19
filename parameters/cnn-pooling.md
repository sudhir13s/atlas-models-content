---
id: parameter/cnn-pooling
type: parameter
slug: cnn-pooling
schema_version: 1.0
title: "Pooling (Max / Average)"
name: pool_size
priority: 8
importance: optional
applies_to: [cnn]
range: "2×2 (typical) — 0 parameters"
controls: "Down-sampling that summarizes each spatial window into one value (max or average). It shrinks the feature maps and, indirectly, the fully-connected head."
formula: "0 parameters — pooling has no learnable weights; it shrinks spatial size, which shrinks the FC head"
if_increased: "A larger pooling window discards more spatial detail per step, shrinking maps faster (and the FC head with them) but losing fine localization."
if_decreased: "Smaller or no pooling keeps spatial resolution — better localization, but a much larger flattened feature map feeding the classifier head."
tags: [cnn, pooling, downsampling, structure, vision]
updated: 2026-07-19
sources:
  - { url: "https://cs231n.github.io/convolutional-networks/", title: "CS231n: Convolutional Neural Networks (Stanford)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1312.4400", title: "Network In Network (global average pooling, Lin et al., 2013)", trust: 0.9 }
---

## Definition
Pooling down-samples a feature map by summarizing each small window into a single value — **max
pooling** takes the largest activation, **average pooling** takes the mean. It reduces spatial size
(e.g. `2×2` pooling halves height and width) and grants small translation invariance.

Pooling has **no learnable parameters.** It is a fixed operation, so it contributes nothing to
parameter count directly — but it strongly affects the size of the fully-connected head downstream.

## What to look for
The indirect effect is the important one: pooling shrinks the flattened feature map that feeds the
classifier, so it controls how large the FC head must be.

- `2×2` max pooling after conv blocks is the classic pattern (halve resolution each step).
- **Global average pooling (GAP)** collapses each entire feature map to one number, removing the FC
  head's parameter explosion — this is why ResNet has a tiny head where VGG has a huge one.

## Calculation
Pooling carries **0 parameters**. Its contribution is entirely indirect, through the head:

$$P_\text{FC head} = (\text{flatten dim}) \times \text{units} + \text{units}$$

Each pooling step divides the spatial dimensions, so it divides the flatten dim. A `2×2` pool cuts the
flatten size by 4×, cutting the first FC layer's parameters by 4× — a large saving even though pooling
itself adds nothing.

## Example
Feed a `128 × 8 × 8` feature map to a 256-unit FC layer: flatten = $128 \times 8 \times 8 = 8192$,
so the layer costs $8192 \times 256 \approx 2.1\text{M}$. Apply one `2×2` pool first (→ `128 × 4 ×
4`, flatten 2048) and it drops to $2048 \times 256 \approx 0.52\text{M}$ — a 4× cut. Replace it with
global average pooling (→ `128`) and the layer is $128 \times 256 = 32\,768$ — a 64× cut, all from a
zero-parameter operation.
