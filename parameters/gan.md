---
id: parameter/gan
type: parameter
slug: gan
schema_version: 1.0
title: "Generative Adversarial Network (GAN) Parameters"
arch: gan
order: 6
color_key: gan
components:
  - { name: "generator depth / channels", rank: 1, importance: critical, badge: "Structure", formula: "Σ transposed-conv params across the up-sampling stack", range: "5 → 8 blocks" }
  - { name: "discriminator depth / channels", rank: 2, importance: critical, badge: "Structure", formula: "Σ conv params (training only, discarded at inference)", range: "5 → 8 blocks" }
  - { name: "transposed-conv (fractionally-strided) filters", rank: 3, importance: critical, badge: "Per Layer", formula: "k² × in_ch × out_ch (up-samples in the generator)", range: "k = 4 typical" }
  - { name: "conv filters (discriminator)", rank: 4, importance: critical, badge: "Per Layer", formula: "k² × in_ch × out_ch (down-samples in the critic)", range: "k = 4 typical" }
  - { name: "base feature width (ngf / ndf)", rank: 5, importance: critical, badge: "Structure", formula: "scales every channel count in G and D", range: "64 → 128" }
  - { name: "latent dim (z)", rank: 6, importance: critical, badge: "Structure", formula: "first projection = z × (C·H·W) of the initial feature map", range: "100 → 512" }
  - { name: "final FC / patch head (discriminator)", rank: 7, importance: important, badge: "Head", formula: "flatten × 1 (real/fake logit) or 1×1 conv to a patch map", range: "small" }
  - { name: "batch normalization (γ, β)", rank: 8, importance: important, badge: "Per Layer", formula: "2 × channels per BN (both networks)", range: "2·C" }
  - { name: "kernel_size (k)", rank: 9, importance: important, badge: "Per Layer", formula: "cost grows with k² (4×4 is the DCGAN default)", range: "3 → 5" }
  - { name: "conv / convT bias", rank: 10, importance: optional, badge: "Per Layer", formula: "+ out_ch; off when BatchNorm follows", range: "0 or out_ch" }
  - { name: "output channels (image)", rank: 11, importance: architectural, badge: "Head", formula: "0 extra params; 3 (RGB) or 1 (grayscale) final channels", range: "1 or 3" }
worked_example:
  label: "DCGAN 64×64 (generator + discriminator)"
  config: { latentDim: 100, kernel: 4, stride: 2, imageSize: "3×64×64", generatorChannels: "1024→512→256→128→3", discriminatorChannels: "3→128→256→512→1024→1", batchNorm: true, convBias: false, activationG: ReLU, activationD: LeakyReLU }
  sources:
    - { url: "https://arxiv.org/abs/1511.06434", title: "Unsupervised Representation Learning with Deep Convolutional GANs (Radford et al., 2015)", trust: 0.95 }
tags: [gan, generative, convolution, parameters, vision]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1406.2661", title: "Generative Adversarial Nets (Goodfellow et al., 2014)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1511.06434", title: "Unsupervised Representation Learning with Deep Convolutional GANs (Radford et al., 2015)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1812.04948", title: "A Style-Based Generator Architecture for GANs (StyleGAN, Karras et al., 2018)", trust: 0.9 }
---

## Overview
A GAN is really two networks trained against each other: a **generator** that maps a random
latent vector `z` to an image, and a **discriminator** (critic) that scores images as real or
fake. Both hold parameters, but they play very different roles in the parameter budget. The
generator up-samples with **transposed (fractionally-strided) convolutions**, growing a tiny
`4×4` feature map up to full resolution; the discriminator is an ordinary CNN that down-samples
with strided convolutions to a single real/fake logit.

The crucial accounting fact: **only the generator survives training.** The discriminator exists
purely to provide a learning signal and is thrown away at inference, so a GAN's *deployment*
parameter count is the generator alone — even though training memory must hold both. A
convolutional GAN's parameters are dominated by the middle layers, where channel counts are
highest and each filter is a `k² × C_in × C_out` volume.

## Components
- **Transposed-conv filters (generator)** carry the same `k² × C_in × C_out` cost as a normal
  convolution; the "transposed" part changes the spatial arithmetic (up-sampling), not the
  parameter formula.
- **Conv filters (discriminator)** mirror the generator in reverse, down-sampling `3 → base →
  … → 1`. Channels typically double at each stride-2 step.
- **Base feature width (ngf / ndf)** is the single scaling lever: every channel count is a
  multiple of it, so parameters scale roughly with its square (both `C_in` and `C_out` grow).
- **Latent dim (z)** sizes only the first projection (`z → C·H·W` of the initial feature map);
  it is a small share of the total.
- **BatchNorm** learns `γ, β` per channel in both networks; conv/convT biases are usually
  disabled because BN absorbs them.
- **Final head** is a single conv (or FC) to one logit in the classic DCGAN; a PatchGAN critic
  instead emits a small grid of logits.

The master formula. Every conv or transposed-conv layer with kernel `k`, `C_in → C_out`
channels costs:

$$P_\text{conv} = k^2 \cdot C_\text{in} \cdot C_\text{out} \; (+\, C_\text{out}\ \text{if bias})$$

BatchNorm adds `2·C_out`. Total = (sum over generator layers) + (sum over discriminator layers),
but only the first sum ships at inference.

## Worked Example
DCGAN 64×64 (`config` above), `z = 100`, all kernels `4×4`, BatchNorm on, conv bias off.

**Generator** (project `z` to `1024×4×4`, then up-sample):
- project `z → 1024·4·4`: $4^2 \times 100 \times 1024 = 1\,638\,400$; BN(1024) $= 2\,048$
- convT `1024 → 512`: $16 \times 1024 \times 512 = 8\,388\,608$; BN(512) $= 1\,024$
- convT `512 → 256`: $16 \times 512 \times 256 = 2\,097\,152$; BN(256) $= 512$
- convT `256 → 128`: $16 \times 256 \times 128 = 524\,288$; BN(128) $= 256$
- convT `128 → 3` (tanh, no BN): $16 \times 128 \times 3 = 6\,144$

Generator total $\approx 12\,658\,432 \approx \mathbf{12.66\text{M}}$.

**Discriminator** (mirror, down-sampling to one logit):
- conv `3 → 128` (no BN): $16 \times 3 \times 128 = 6\,144$
- conv `128 → 256`: $16 \times 128 \times 256 = 524\,288$; BN(256) $= 512$
- conv `256 → 512`: $16 \times 256 \times 512 = 2\,097\,152$; BN(512) $= 1\,024$
- conv `512 → 1024`: $16 \times 512 \times 1024 = 8\,388\,608$; BN(1024) $= 2\,048$
- conv `1024 → 1` (`4×4 → 1×1` logit, +1 bias): $16 \times 1024 \times 1 + 1 = 16\,385$

Discriminator total $\approx 11\,036\,161 \approx \mathbf{11.04\text{M}}$.

Combined training footprint $\approx \mathbf{23.7\text{M}}$ parameters, but the deployed
generator is only ~12.7M — the discriminator's ~11M is scaffolding discarded once training
ends. As in any convolutional stack, the two middle layers (`8.39M + 2.10M`) hold the
overwhelming majority of each network's weights.
