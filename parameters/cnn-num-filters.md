---
id: parameter/cnn-num-filters
type: parameter
slug: cnn-num-filters
schema_version: 1.0
title: "Number of Filters (out_channels)"
name: out_channels
priority: 1
importance: critical
applies_to: [cnn]
range: "16 → 512"
controls: "How many feature detectors a convolution layer learns. It is the width of a conv layer and the primary lever on its parameter count."
formula: "params = k² × in_channels × out_channels + out_channels (bias)"
if_increased: "More feature maps → richer representations and higher capacity, but the weight count grows linearly with out_channels (and it becomes the next layer's in_channels, compounding downstream)."
if_decreased: "A thinner, cheaper layer, but fewer distinct patterns can be detected at that depth — a bottleneck on what the network can represent."
tags: [cnn, convolution, channels, filters, width, parameters, vision]
updated: 2026-07-19
sources:
  - { url: "https://cs231n.github.io/convolutional-networks/", title: "CS231n: Convolutional Neural Networks (Stanford)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1409.1556", title: "Very Deep Convolutional Networks (VGG, Simonyan & Zisserman, 2014)", trust: 0.9 }
---

## Definition
`out_channels` is the number of **filters** (feature detectors) a convolution layer learns. Each
filter is a small `k × k × in_channels` volume that slides across the input and produces one output
feature map, so `out_channels` is literally the width — the number of output maps — of the layer.

It is the primary lever on a conv layer's size: every filter is an independent stack of weights, and
this layer's `out_channels` becomes the next layer's `in_channels`, so widening one layer compounds
into the one that follows.

## What to look for
Channel counts typically **grow with depth** as spatial resolution shrinks — a common pattern is
32 → 64 → 128 → 256, doubling after each down-sampling step:

- Early layers: few channels (16–64) over large spatial maps.
- Deep layers: many channels (256–512) over small maps.

This "narrow-and-tall → wide-and-short" shape keeps the compute roughly balanced across layers while
letting deeper layers hold more abstract features.

## Calculation
For one conv layer with kernel `k`, `C_in` input and `C_out` output channels (`out_channels`):

$$P_\text{conv} = k^2 \cdot C_\text{in} \cdot C_\text{out} + C_\text{out}$$

The count grows **linearly** in `out_channels` for a fixed input, but because this layer's `C_out`
is the next layer's `C_in`, widening a middle layer inflates two layers at once — the reason the
high-channel middle of a CNN holds most of its convolutional weights.

## Example
A `3×3` conv from 64 to 128 channels: $9 \times 64 \times 128 + 128 = 73\,856$ parameters. Halve the
output to 64 channels and it drops to $9 \times 64 \times 64 + 64 = 36\,928$ — roughly half. Because
that same layer feeds the next one, the following `3×3` conv also halves its `in_channels`, so a
single width cut ripples into a ~2× saving across the pair.
