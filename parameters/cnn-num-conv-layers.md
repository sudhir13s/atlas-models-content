---
id: parameter/cnn-num-conv-layers
type: parameter
slug: cnn-num-conv-layers
schema_version: 1.0
title: "Number of Convolutional Layers (Depth)"
name: num_conv_layers
priority: 2
importance: critical
applies_to: [cnn]
range: "3 → 150+"
controls: "How many convolution layers are stacked — the depth of the network. Deeper stacks grow the receptive field and let features become more abstract."
formula: "total = Σ per-layer params over all conv layers (plus the FC head)"
if_increased: "A larger receptive field and more abstract features, but linearly more parameters and compute, and harder optimization without residual connections."
if_decreased: "Cheaper and easier to train, but a small receptive field and shallow feature hierarchy — the network sees only local patterns."
tags: [cnn, convolution, depth, structure, parameters, vision]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/1409.1556", title: "Very Deep Convolutional Networks (VGG, Simonyan & Zisserman, 2014)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1512.03385", title: "Deep Residual Learning for Image Recognition (ResNet, He et al., 2015)", trust: 0.95 }
---

## Definition
The number of convolutional layers is the **depth** of the network — how many conv stages the input
passes through before reaching the classifier head. Each additional layer composes on the last,
enlarging the **receptive field** (how much of the input one output unit sees) and lifting features
from edges → textures → parts → objects.

Depth multiplies parameters: the total conv cost is the sum over every layer, so stacking more layers
adds their weights directly.

## What to look for
Depth ranges enormously by architecture generation:

- **Classic (VGG-16/19):** 13–19 conv layers.
- **Residual (ResNet-50/101/152):** 50–152 layers, made trainable by skip connections.

Past ~20 layers, plain stacks stop improving (vanishing gradients); **residual connections** are what
let networks go to 100+ layers, by giving gradients an identity shortcut back through the depth.

## Calculation
Depth does not have its own formula — it is the summation bound. With per-layer cost
$P_\ell = k^2 C_{\text{in},\ell} C_{\text{out},\ell} + C_{\text{out},\ell}$:

$$P_\text{conv total} = \sum_{\ell=1}^{L} P_\ell$$

Adding a layer of the same width adds roughly one more $P_\ell$ term — a linear growth in `L`, in
contrast to the quadratic growth from widening channels.

## Example
VGG-16 stacks 13 conv layers and, with its three large fully-connected head layers, reaches ~138M
parameters. ResNet-50 has 50 conv layers but only ~25.6M parameters — far deeper yet far smaller,
because it uses narrow `1×1` bottleneck convolutions and replaces VGG's massive FC head with global
average pooling. Depth and parameter count are correlated but not the same thing.
