---
id: parameter/gan-feature-map-base
type: parameter
slug: gan-feature-map-base
schema_version: 1.0
title: "Base Feature Width (ngf / ndf)"
name: ngf
priority: 1
importance: critical
applies_to: [gan]
range: "64 → 128"
controls: "The base channel count that every layer of the generator (ngf) and discriminator (ndf) is a multiple of. It is the single scaling lever for a GAN's size."
formula: "every conv's channels = multiple of ngf/ndf, so params scale ≈ ngf² (both C_in and C_out grow)"
if_increased: "Wider feature maps → more capacity and sharper images, but parameters grow with the square of the base width because both input and output channels scale together."
if_decreased: "A smaller, faster GAN, but thinner feature maps limit image quality and the diversity the generator can produce."
tags: [gan, generative, channels, width, parameters, vision]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/1511.06434", title: "Unsupervised Representation Learning with Deep Convolutional GANs (Radford et al., 2015)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1812.04948", title: "A Style-Based Generator Architecture for GANs (StyleGAN, Karras et al., 2018)", trust: 0.9 }
---

## Definition
`ngf` (number of generator feature maps) and `ndf` (its discriminator counterpart) are the **base
channel widths** that every layer's channel count is a multiple of. A DCGAN generator's channels run
`8·ngf → 4·ngf → 2·ngf → ngf → 3`, so one number scales the whole network.

It is the GAN's master width lever — the analogue of `hidden_size` in a transformer — and it moves
parameter count **quadratically**.

## What to look for
The base width is the primary quality-vs-cost dial for both networks:

- **ngf/ndf = 64:** the DCGAN default, good for 64×64 images.
- **ngf/ndf = 128+:** higher-resolution or higher-fidelity generation.

`ngf` and `ndf` are usually kept equal so the generator and discriminator stay balanced — if the
discriminator badly out-powers the generator, training collapses. Modern GANs (StyleGAN) use per-stage
channel maxima instead of one flat base, but the "one width scales everything" idea is unchanged.

## Calculation
Because a middle conv maps `a·ngf → b·ngf` channels, its cost has **two** factors of the base width:

$$P_\text{conv} = k^2 \cdot (a\cdot\text{ngf}) \cdot (b\cdot\text{ngf}) = k^2\,ab\,\text{ngf}^2$$

So the whole network scales with `ngf²` — doubling the base width roughly **quadruples** the
parameter count. The latent projection and the final image conv are the only layers not fully scaled
by it.

## Example
A DCGAN middle layer, transposed-conv `4·ngf → 2·ngf` with `4×4` kernels at `ngf = 64` (i.e. `256 →
128`): $16 \times 256 \times 128 = 524\,288$. Raise the base to `ngf = 128` (`512 → 256`): $16 \times
512 \times 256 = 2\,097\,152$ — a 4× jump for a 2× width, the quadratic signature. This is why the
base feature width dominates a convolutional GAN's total size.
