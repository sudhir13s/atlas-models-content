---
id: parameter-calculator/cnn
type: parameter-calculator
slug: cnn
schema_version: 3.1
title: Convolutional Network (CNN) Parameters
arch: cnn
order: 4
color_key: cnn
components:
- name: out_channels (filters per conv)
  rank: 1
  importance: critical
  badge: Per Layer
  formula: params = k² × in_ch × out_ch + out_ch
  range: 16 → 512
- name: kernel_size (k)
  rank: 2
  importance: critical
  badge: Per Layer
  formula: cost grows with k² (3×3 is the default)
  range: 1 → 7
- name: in_channels
  rank: 3
  importance: critical
  badge: Per Layer
  formula: multiplies every filter's weight count
  range: 3 → 512
- name: fully-connected head (classifier)
  rank: 4
  importance: important
  badge: Head
  formula: flatten × units + units; often dominates the total
  range: task-dependent
- name: network depth (num conv layers)
  rank: 5
  importance: important
  badge: Structure
  formula: total = Σ per-layer params
  range: 3 → 150+
- name: conv bias
  rank: 6
  importance: optional
  badge: Per Layer
  formula: + out_ch per conv; usually off when BatchNorm follows
  range: 0 or out_ch
- name: batch normalization (γ, β)
  rank: 7
  importance: important
  badge: Per Layer
  formula: 2 × out_ch trainable (running mean/var are buffers)
  range: 2·out_ch
- name: pooling (max / average)
  rank: 8
  importance: architectural
  badge: Per Layer
  formula: 0 params; shrinks spatial size → shrinks FC head
  range: '0'
- name: global average pooling
  rank: 9
  importance: architectural
  badge: Head
  formula: 0 params; collapses maps, removes the FC-head explosion
  range: '0'
- name: stride / padding
  rank: 10
  importance: architectural
  badge: Per Layer
  formula: 0 params; changes spatial size → changes FC head size
  range: stride 1–2
worked_example:
  label: Small CIFAR-10 CNN (3 conv + 2 FC)
  config:
    inputShape: 3×32×32
    conv1: 3×3, 3→32
    conv2: 3×3, 32→64
    conv3: 3×3, 64→64
    pooling: 2×2 max after each conv
    flatten: 64×4×4 = 1024
    fc1: 1024→64
    fc2: 64→10
    classes: 10
  sources:
  - url: https://cs231n.github.io/convolutional-networks/
    title: 'CS231n: Convolutional Neural Networks (Stanford)'
    trust: 0.9
tags:
- cnn
- convolution
- parameters
- vision
updated: '2026-07-17'
sources:
- url: https://cs231n.github.io/convolutional-networks/
  title: 'CS231n: Convolutional Neural Networks (Stanford)'
  trust: 0.9
- url: http://yann.lecun.com/exdb/publis/pdf/lecun-98.pdf
  title: Gradient-Based Learning Applied to Document Recognition (LeCun et al., 1998)
  trust: 0.9
- url: https://arxiv.org/abs/1409.1556
  title: Very Deep Convolutional Networks (VGG, Simonyan & Zisserman, 2014)
  trust: 0.9
---

## Overview

A convolutional network's parameters live in two very different kinds of layer. **Convolution
layers** share a small set of filter weights across every spatial position, so their parameter count
is independent of the input image size — a 3×3 filter over a 224×224 image and over a 32×32 image
cost the same. **Fully-connected (dense) head layers** connect every input to every output, so their
count depends on the flattened feature-map size and frequently dwarfs the convolutional stack in
classic architectures (VGG-16 keeps ~90% of its 138M parameters in three FC layers).

The key intuition: convolutions are cheap in parameters but expensive in compute (each filter is
reused at every pixel); dense layers are the opposite. Weight sharing is exactly why CNNs generalize
on images with far fewer parameters than a fully-connected net of comparable capacity.

## Components

- **out_channels** is the number of filters in a conv layer and the primary lever on its size: each
  filter is a `k × k × in_channels` volume plus one bias.
- **kernel_size (k)** enters as `k²`; 3×3 is the modern default because stacking two 3×3 layers has
  the receptive field of one 5×5 at fewer parameters and more nonlinearity.
- **in_channels** multiplies every filter — this is why channel counts (and therefore cost) grow
  deeper into the network even as spatial resolution shrinks.
- **fully-connected head** flattens the final feature map and maps it to class logits; its size is
  `flatten_dim × units`, which can be enormous if pooling has not reduced the spatial dimensions.
- **stride / padding** carry no parameters but control the spatial size fed to the head, indirectly
  setting the head's parameter count. Global average pooling removes the head-size explosion entirely.

The master formulas. A conv layer with kernel `k`, `C_in` input and `C_out` output channels:

$$P_\text{conv} = k^2 \cdot C_\text{in} \cdot C_\text{out} + C_\text{out}$$

A dense layer from `n_in` to `n_out` units:

$$P_\text{fc} = n_\text{in} \cdot n_\text{out} + n_\text{out}$$

Total = sum over all conv and FC layers.

## Worked Example

The small CIFAR-10 CNN (`config` above), input 3×32×32, a 2×2 max-pool after each conv so spatial
size halves 32 → 16 → 8 → 4:

- **conv1** (3×3, 3→32): $9 \times 3 \times 32 + 32 = 896$
- **conv2** (3×3, 32→64): $9 \times 32 \times 64 + 64 = 18\,496$
- **conv3** (3×3, 64→64): $9 \times 64 \times 64 + 64 = 36\,928$
- **fc1** (flatten $64 \times 4 \times 4 = 1024$ → 64): $1024 \times 64 + 64 = 65\,600$
- **fc2** (64 → 10): $64 \times 10 + 10 = 650$

Total $= 896 + 18\,496 + 36\,928 + 65\,600 + 650 = \mathbf{122\,570}$ parameters. Note that the two
FC layers ($66\,250$) hold more than the entire convolutional stack ($56\,320$) even in this tiny
network — the classic signature of a CNN's parameter distribution, and the motivation for replacing
large FC heads with global average pooling in modern designs.
