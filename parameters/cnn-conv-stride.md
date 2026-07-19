---
id: parameter/cnn-conv-stride
type: parameter
slug: cnn-conv-stride
schema_version: 1.0
title: "Convolution Stride"
name: stride
priority: 9
importance: optional
applies_to: [cnn]
range: "1 (dense) → 2 (down-sampling)"
controls: "How many pixels a filter moves between applications. Stride > 1 down-samples the output, an alternative to pooling."
formula: "0 parameters — stride changes the output spatial size, which changes the FC head size"
if_increased: "A larger stride down-samples more aggressively, shrinking feature maps (and the FC head) and cutting compute — but discarding spatial detail."
if_decreased: "Stride 1 keeps full resolution and dense overlap between filter applications, preserving detail at higher compute and a larger downstream head."
tags: [cnn, convolution, stride, downsampling, structure, vision]
updated: 2026-07-19
sources:
  - { url: "https://cs231n.github.io/convolutional-networks/", title: "CS231n: Convolutional Neural Networks (Stanford)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1412.6806", title: "Striving for Simplicity: The All Convolutional Net (Springenberg et al., 2014)", trust: 0.9 }
---

## Definition
Stride is how far a filter shifts between successive applications. **Stride 1** applies the filter at
every pixel (dense, full-resolution output); **stride 2** skips every other position, halving the
output height and width — a learnable alternative to pooling for down-sampling.

Like pooling and padding, stride is an **architectural knob with zero parameters.** It changes the
output spatial size, not the filter weights.

## What to look for
Stride is one of two ways to down-sample; the choice shapes the network:

- **Strided convolution** learns *how* to down-sample (the filter weights do the reduction) — favored
  by "all-convolutional" and modern designs.
- **Pooling** down-samples with a fixed rule (max/avg) and no parameters.

Either way, the spatial reduction shrinks the flattened feature map feeding the classifier, so stride
indirectly sets the FC head's size — the same lever as pooling.

## Calculation
Stride adds **no parameters**. Its effect on the model's size is indirect, via the output dimensions:

$$H_\text{out} = \left\lfloor \frac{H_\text{in} + 2p - k}{s} \right\rfloor + 1$$

for input height `H_in`, padding `p`, kernel `k`, stride `s`. A stride-2 layer roughly halves each
spatial dimension, cutting the downstream flatten size (and its FC head) by ~4×.

## Example
A stride-2 `3×3` conv from 64 to 128 channels has exactly the same $9 \times 64 \times 128 + 128 =
73\,856$ weights as its stride-1 twin — stride costs nothing. But its output is `~½ × ½` the spatial
size, so the feature map handed to the next stage (and ultimately the FC head) is ~4× smaller,
saving parameters everywhere downstream while the conv layer's own count is unchanged.
