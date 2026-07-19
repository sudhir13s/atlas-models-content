---
id: parameter/cnn-kernel-size
type: parameter
slug: cnn-kernel-size
schema_version: 1.0
title: "Kernel Size (k)"
name: kernel_size
priority: 3
importance: critical
applies_to: [cnn]
range: "1 → 7"
controls: "The spatial extent of each filter — how large a patch a convolution looks at in one step. It enters the parameter count as k²."
formula: "params per filter = k² × in_channels; layer cost scales with k²"
if_increased: "A larger receptive field per layer, but the weight count grows with the square of the kernel size — a 5×5 filter costs ~2.8× a 3×3."
if_decreased: "Cheaper filters and a smaller per-layer receptive field; 1×1 kernels mix channels only, with no spatial context."
tags: [cnn, convolution, kernel, receptive-field, parameters, vision]
updated: 2026-07-19
sources:
  - { url: "https://cs231n.github.io/convolutional-networks/", title: "CS231n: Convolutional Neural Networks (Stanford)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1409.1556", title: "Very Deep Convolutional Networks (VGG, Simonyan & Zisserman, 2014)", trust: 0.9 }
---

## Definition
`kernel_size` (`k`) is the height and width of each filter — the size of the input patch a convolution
reads at every position. A `3×3` kernel looks at a 9-pixel neighborhood; a `1×1` kernel looks at a
single pixel across all channels (a pure channel mixer).

Because each filter is a `k × k × in_channels` volume, the parameter count scales with **k²** — kernel
size is a quadratic, not linear, cost lever.

## What to look for
`3×3` is the modern default, for a precise reason: **stacking two 3×3 convolutions has the receptive
field of one 5×5 but uses fewer parameters and adds a nonlinearity between them.**

- `1×1`: channel projection / bottleneck (no spatial mixing) — used in ResNet and Inception.
- `3×3`: the workhorse — nearly all of VGG and ResNet.
- `5×5`, `7×7`: only in early layers (e.g. ResNet's `7×7` stem) to grab wide context cheaply once.

## Calculation
For a layer with kernel `k`, `C_in` and `C_out` channels:

$$P_\text{conv} = k^2 \cdot C_\text{in} \cdot C_\text{out} + C_\text{out}$$

The `k²` factor is why kernel size matters: going `3×3 → 5×5` multiplies the weight matrix by
$25/9 \approx 2.8$, and `3×3 → 7×7` by $49/9 \approx 5.4$, for the same channel counts.

## Example
A `C_in = C_out = 128` conv: at `3×3` it is $9 \times 128 \times 128 = 147\,456$ weights; at `5×5` it
is $25 \times 128 \times 128 = 409\,600$ — 2.8× larger. Two stacked `3×3` layers ($2 \times 147\,456
= 294\,912$) match a single `5×5`'s receptive field with **fewer** weights than the one `5×5`, plus an
extra nonlinearity — exactly the VGG design argument for preferring small kernels.
