---
id: parameter/gan-latent-dim
type: parameter
slug: gan-latent-dim
schema_version: 1.0
title: "Latent Dimension (z)"
name: latent_dim
priority: 4
importance: important
applies_to: [gan]
range: "100 → 512"
controls: "The size of the random noise vector the generator maps into an image. It sets the dimensionality of the space of generatable outputs and the size of the first projection only."
formula: "first projection = z × (C · H · W) of the initial feature map; a small share of the total"
if_increased: "A richer latent space can encode more distinct factors of variation, but the only parameter cost is a larger first projection — the rest of the generator is unchanged."
if_decreased: "A smaller latent space is marginally cheaper and can bottleneck output diversity if too small; below the data's true degrees of freedom, modes get dropped."
tags: [gan, generative, latent, noise, parameters, vision]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/1406.2661", title: "Generative Adversarial Nets (Goodfellow et al., 2014)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1511.06434", title: "Unsupervised Representation Learning with Deep Convolutional GANs (Radford et al., 2015)", trust: 0.95 }
---

## Definition
The latent dimension `z` is the length of the **random noise vector** sampled (typically from a
standard Gaussian) as the generator's input. The generator learns a mapping from this latent space to
the image manifold, so `z` sets the dimensionality of the space of possible outputs.

In parameter terms it touches only **one layer** — the first projection that expands `z` into the
initial feature map — so it is a small share of the total, unlike the base width or depth.

## What to look for
The latent size should roughly match the data's true **degrees of freedom**:

- **100 (DCGAN):** the classic default, ample for simple datasets.
- **512 (StyleGAN):** larger, disentangled latent (StyleGAN further maps `z` through an MLP into an
  intermediate `w` space for better control).

Too small a `z` bottlenecks diversity — the generator physically cannot represent enough distinct
outputs. Beyond the data's real complexity, extra latent dimensions add little.

## Calculation
`z` sizes only the initial projection from the latent vector to the first feature map `C × H × W`:

$$P_\text{proj} = z \cdot (C \cdot H \cdot W)\quad(\text{or } k^2 \cdot z \cdot C\text{ for a transposed-conv projection})$$

Every later layer's cost is independent of `z`. This makes the latent dimension the **least** impactful
size lever among the GAN's structural knobs.

## Example
A DCGAN projecting `z = 100` into a `1024 × 4 × 4` map via a `4×4` transposed conv costs $16 \times
100 \times 1024 \approx 1.64\text{M}$ — meaningful, but only ~13% of the ~12.7M generator. Raising `z`
to 512 grows just that layer to $16 \times 512 \times 1024 \approx 8.39\text{M}$; every other block is
untouched. Contrast the base feature width, which scales *every* block quadratically.
