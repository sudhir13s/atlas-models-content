---
id: parameter/cnn-batch-norm
type: parameter
slug: cnn-batch-norm
schema_version: 1.0
title: "Batch Normalization (γ, β)"
name: batch_norm
priority: 6
importance: important
applies_to: [cnn]
range: "2 × channels per BN layer"
controls: "Whether each conv layer is followed by batch normalization, which normalizes activations and learns a per-channel scale and shift. It stabilizes and speeds up training."
formula: "trainable params = 2 × channels (γ scale + β shift); running mean/var are buffers, not parameters"
if_increased: "Adding BN improves training stability and lets you use higher learning rates, at the cost of only 2 params per channel — and it usually lets you drop the conv bias."
if_decreased: "Removing BN saves a negligible 2·C params but often hurts convergence; alternatives (GroupNorm, LayerNorm) are used when batch sizes are tiny."
tags: [cnn, normalization, batchnorm, training, parameters, vision]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/1502.03167", title: "Batch Normalization (Ioffe & Szegedy, 2015)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1512.03385", title: "Deep Residual Learning for Image Recognition (ResNet, He et al., 2015)", trust: 0.95 }
---

## Definition
Batch normalization (BN) normalizes each channel's activations to zero mean and unit variance over
the batch, then applies a **learned per-channel scale `γ` and shift `β`**. It smooths the loss
landscape, allowing higher learning rates and faster, more stable convergence in deep CNNs.

Only `γ` and `β` are trainable parameters. The running mean and variance BN tracks for inference are
**buffers**, not parameters — they are not updated by gradient descent.

## What to look for
BN almost always sits **between a conv and its activation** (Conv → BN → ReLU) and is standard in
every modern CNN from ResNet onward. Two practical consequences:

- The preceding conv's **bias is redundant** (BN's `β` absorbs it), so `bias=False` is set on the conv.
- BN behaves differently in **train vs eval mode** — training uses batch statistics, inference uses
  the stored running statistics. Forgetting `model.eval()` is a classic source of wrong predictions.

## Calculation
For a BN layer over `C` channels, the trainable count is small and fixed:

$$P_\text{BN} = 2C \quad(\gamma \in \mathbb{R}^C,\; \beta \in \mathbb{R}^C)$$

The running mean and variance add another `2C` values stored as buffers, but these do **not** count as
model parameters. BN's contribution to total size is therefore tiny — its value is in optimization,
not capacity.

## Example
A conv with 256 output channels followed by BN adds $2 \times 256 = 512$ trainable parameters against
the conv's hundreds of thousands — well under 0.1% of the layer. In a full ResNet-50 all BN layers
together are a few tens of thousands of parameters out of 25.6M. Negligible in size, decisive in
whether a 50-layer network trains at all.
