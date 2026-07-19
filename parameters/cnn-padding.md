---
id: parameter/cnn-padding
type: parameter
slug: cnn-padding
schema_version: 1.0
title: "Padding"
name: padding
priority: 10
importance: optional
applies_to: [cnn]
range: "0 (valid) → k//2 (same)"
controls: "How many border pixels are added around the input before convolving. It sets whether the output keeps the input's spatial size and how edge pixels are treated."
formula: "0 parameters — padding preserves spatial size, which keeps the FC head's size predictable"
if_increased: "'Same' padding keeps output resolution equal to input, preserving edge information and allowing deeper stacks without maps shrinking to nothing."
if_decreased: "'Valid' (no) padding shrinks the map by k−1 pixels per layer and under-samples the borders — resolution is lost with every layer."
tags: [cnn, convolution, padding, structure, vision]
updated: 2026-07-19
sources:
  - { url: "https://cs231n.github.io/convolutional-networks/", title: "CS231n: Convolutional Neural Networks (Stanford)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1603.07285", title: "A Guide to Convolution Arithmetic for Deep Learning (Dumoulin & Visin, 2016)", trust: 0.9 }
---

## Definition
Padding adds a border of extra pixels (usually zeros) around the input before the filter is applied.
Without it, each convolution shrinks the feature map by `k − 1` pixels and samples corner pixels far
fewer times than center ones. Padding controls whether output size is **preserved** and how edges are
handled.

It is an **architectural knob with zero parameters** — it changes the output geometry, not the
weights.

## What to look for
Two modes cover almost all use:

- **"Same" padding** (`p = k//2` for odd `k`): output spatial size equals input. Standard in VGG /
  ResNet so a deep stack doesn't shrink the map away, and so border pixels get full filter coverage.
- **"Valid" padding** (`p = 0`): no border added; the map shrinks each layer. Used when boundary
  effects don't matter or a deliberate size reduction is wanted.

Keeping resolution stable also keeps the downstream flatten size predictable, which stabilizes the FC
head's parameter count.

## Calculation
Padding adds **no parameters**. It enters only the output-size formula:

$$H_\text{out} = \left\lfloor \frac{H_\text{in} + 2p - k}{s} \right\rfloor + 1$$

With "same" padding (`p = k//2`, `s = 1`), `H_out = H_in` — the term $2p - k$ vanishes for odd `k`,
holding size constant across the layer. The filter weight count $k^2 C_\text{in} C_\text{out}$ is
independent of `p`.

## Example
A `3×3` conv with `padding = 1` on a `32×32` input returns a `32×32` output (`same`); with `padding =
0` it returns `30×30` (`valid`), losing a one-pixel border. Neither changes the layer's weights — both
cost $9 \times C_\text{in} \times C_\text{out} + C_\text{out}$ — but only "same" padding lets you
stack many `3×3` layers without the feature map collapsing, which is why deep CNNs pad by default.
