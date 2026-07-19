---
id: parameter/gan-generator-depth
type: parameter
slug: gan-generator-depth
schema_version: 1.0
title: "Generator Depth / Channels"
name: generator_depth
priority: 2
importance: critical
applies_to: [gan]
range: "5 → 8 up-sampling blocks"
controls: "How many transposed-convolution blocks the generator uses to grow a latent vector into a full-resolution image. Depth is set by the target resolution."
formula: "total = Σ transposed-conv params across the up-sampling stack (this is what ships at inference)"
if_increased: "More up-sampling stages reach higher output resolutions and add capacity, but each block adds parameters — and the generator is the network that is actually deployed."
if_decreased: "A shallower generator is smaller and faster but caps the output resolution and the detail it can synthesize."
tags: [gan, generative, generator, depth, structure, parameters, vision]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/1406.2661", title: "Generative Adversarial Nets (Goodfellow et al., 2014)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1511.06434", title: "Unsupervised Representation Learning with Deep Convolutional GANs (Radford et al., 2015)", trust: 0.95 }
---

## Definition
Generator depth is how many **transposed-convolution (up-sampling) blocks** the generator stacks to
transform a latent vector `z` into an image. Each block roughly doubles spatial resolution while
halving channels, so depth is tied to the target output size: `4×4 → 8×8 → … → 64×64` needs four
up-sampling blocks.

This is the network that **actually ships** — at inference a GAN is only its generator, so its depth
and channels set the deployed parameter count.

## What to look for
Depth is essentially fixed by the resolution you want, since each block doubles the spatial size:

- **64×64 (DCGAN):** ~4–5 up-sampling blocks.
- **256×256 / 1024×1024 (StyleGAN):** 7–8 blocks (or more), with progressive growth.

The largest blocks are the **early, high-channel** ones near `4×4`, not the final near-image layers —
the same "middle layers dominate" rule as any convolutional stack.

## Calculation
Depth is the summation over the up-sampling blocks; each transposed conv costs the same as a normal
convolution:

$$P_\text{gen} = \sum_{\text{blocks}} \big(k^2 \cdot C_\text{in} \cdot C_\text{out}\ [+\,C_\text{out}]\big) + \text{BatchNorm}(2C)$$

Adding a block adds one such term. Because early blocks carry the most channels, they dominate — the
generator's size is front-loaded toward its low-resolution stages.

## Example
A DCGAN generator (`ngf = 64`, `4×4` kernels) up-samples `1024 → 512 → 256 → 128 → 3`. The two
largest blocks are the latent projection `z → 1024·4·4` ($\approx 1.64\text{M}$) and `1024 → 512`
($16 \times 1024 \times 512 = 8.39\text{M}$); the whole generator is ~12.7M parameters. Adding an
extra up-sampling stage for 128×128 output appends another multi-million-parameter block — and, unlike
the discriminator, that cost is paid at deployment.
