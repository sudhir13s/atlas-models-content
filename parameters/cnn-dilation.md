---
id: parameter/cnn-dilation
type: parameter
slug: cnn-dilation
schema_version: 1.0
title: "Dilation (Atrous Convolution)"
name: dilation
priority: 7
importance: optional
applies_to: [cnn]
range: "1 (standard) → 2, 4, 8+"
controls: "The spacing inserted between kernel taps. It enlarges the receptive field without adding parameters or reducing spatial resolution."
formula: "0 extra parameters — a k×k dilated filter still has k² taps; only their spacing changes"
if_increased: "A larger receptive field for the same number of weights, useful for dense prediction — but too much dilation causes 'gridding' artifacts where nearby pixels are ignored."
if_decreased: "Dilation 1 is a standard dense convolution; smaller spacing means a tighter, contiguous receptive field."
tags: [cnn, convolution, dilation, atrous, receptive-field, vision]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/1511.07122", title: "Multi-Scale Context Aggregation by Dilated Convolutions (Yu & Koltun, 2015)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1606.00915", title: "DeepLab: Semantic Segmentation with Atrous Convolution (Chen et al., 2016)", trust: 0.9 }
---

## Definition
Dilation (also called **atrous** convolution) inserts gaps between the taps of a filter. A `3×3`
kernel with dilation 2 still has 9 weights, but they sample pixels 2 apart, covering a `5×5` region.
It enlarges the **receptive field** while keeping the parameter count and output resolution unchanged.

This is a purely **architectural knob: it carries zero parameters.** It changes *where* the existing
`k²` taps read from, not how many taps there are.

## What to look for
Dilation is the tool of choice for **dense prediction** (semantic segmentation), where you want wide
context but must keep full spatial resolution:

- Standard down-sampling (pooling/stride) grows the receptive field but throws away resolution.
- Dilated convolution grows it while **preserving** resolution — the DeepLab insight.

The pitfall is **gridding**: stacking equal dilations makes the sampled pixels never overlap, leaving
blind spots. Fixes use mixed dilation rates (e.g. 1, 2, 3) so coverage stays contiguous.

## Calculation
Dilation adds **no parameters**. A `k × k` filter with dilation `d` has the same `k²` weights per
input channel; only its effective footprint changes:

$$\text{effective size} = k + (k - 1)(d - 1)$$

So a `3×3` kernel at dilation 2 covers `3 + 2·1 = 5` pixels per side — a `5×5` receptive field at
`3×3` cost. The weight formula $k^2 \cdot C_\text{in} \cdot C_\text{out}$ is untouched.

## Example
A `3×3` conv at dilation 2 has the same $9 \times C_\text{in} \times C_\text{out}$ weights as an
ordinary `3×3`, yet sees as much context as a `5×5` (which would cost 2.8× more). DeepLab stacks such
atrous convolutions to reach receptive fields spanning much of the image while keeping the feature map
at full resolution for pixel-accurate segmentation — capacity-free context expansion.
