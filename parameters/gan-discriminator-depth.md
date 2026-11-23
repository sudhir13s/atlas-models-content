---
id: parameter/gan-discriminator-depth
type: parameter
slug: gan-discriminator-depth
schema_version: 3.1
title: Discriminator Depth / Channels
name: discriminator_depth
priority: 3
importance: critical
applies_to:
- gan
range: 5 → 8 down-sampling blocks
controls: How many strided-convolution blocks the discriminator uses to compress an image down to a real/fake score. It exists only during training.
formula: total = Σ conv params across the down-sampling stack (training-only; discarded at inference)
if_increased: A more powerful critic gives a sharper training signal, but each block adds parameters to the training footprint — none of which is deployed.
if_decreased: A weaker critic is cheaper to train, but if it is too weak the generator has nothing to beat, and if too strong relative to G, training collapses.
tags:
- gan
- generative
- discriminator
- critic
- depth
- structure
- parameters
- vision
updated: '2026-07-19'
sources:
- url: https://arxiv.org/abs/1406.2661
  title: Generative Adversarial Nets (Goodfellow et al., 2014)
  trust: 0.95
- url: https://arxiv.org/abs/1511.06434
  title: Unsupervised Representation Learning with Deep Convolutional GANs (Radford et al., 2015)
  trust: 0.95
---

## Definition

Discriminator depth is how many **strided-convolution (down-sampling) blocks** the discriminator (the
"critic") stacks to compress an image into a single real/fake logit. It mirrors the generator in
reverse: `3 → base → 2·base → … → 1`, halving spatial size and doubling channels each step.

The key accounting fact: the discriminator is **scaffolding**. It provides the training signal and is
**thrown away at inference**, so its parameters count toward training memory but not deployment size.

## What to look for

The discriminator is usually kept roughly **balanced** with the generator — comparable depth and base
width (`ndf ≈ ngf`):

- Too weak a critic → the generator learns from a poor signal and quality stalls.
- Too strong a critic → gradients to the generator vanish and training collapses (mode collapse).

Depth tracks image resolution the same way the generator's does — one down-sampling block per halving
of spatial size.

## Calculation

Depth sums the down-sampling convolutions, each a standard conv cost:

$$P_\text{disc} = \sum_{\text{blocks}} \big(k^2 \cdot C_\text{in} \cdot C_\text{out}\ [+\,C_\text{out}]\big) + \text{BatchNorm}(2C)$$

This adds to the **training** footprint only. Total training memory holds `P_gen + P_disc`; the
shipped model holds `P_gen` alone.

## Example

A DCGAN discriminator (`ndf = 64`, `4×4` kernels) down-samples `3 → 128 → 256 → 512 → 1024 → 1`,
totaling ~11.0M parameters — with the deepest block `512 → 1024` alone at $16 \times 512 \times 1024 =
8.39\text{M}$. Combined with the ~12.7M generator, training must hold ~23.7M parameters, but only the
generator's ~12.7M is deployed: the discriminator's ~11M vanishes the moment training ends.
