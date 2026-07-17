---
id: architecture-era/gan
type: architecture-era
slug: gan
schema_version: 1.0
title: "Generative Adversarial Networks"
name: "Generative Adversarial Network"
era: foundation
era_range: "2014–2020"
year: 2014
family: generation
core_innovation: "Train a generator against a discriminator in a minimax game, learning to synthesize data with no explicit likelihood."
description: "The adversarial generator-vs-discriminator framework that drove a decade of photorealistic image synthesis."
key_features: ["Generator vs. discriminator minimax game", "Implicit (likelihood-free) generative model", "Sharp, high-fidelity samples", "Adversarial training dynamics"]
key_models: ["DCGAN (2015)", "StyleGAN (2019)", "BigGAN (2018)"]
paper_title: "Generative Adversarial Nets"
paper_url: "https://arxiv.org/abs/1406.2661"
influenced_by: []
led_to: [architecture-era/diffusion]
tags: [gan, generation, adversarial, image-synthesis]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1406.2661", title: "Generative Adversarial Nets (Goodfellow et al., 2014)", trust: 0.95 }
---

## What Changed
Generative models before GANs typically optimised an explicit likelihood and tended to produce blurry
samples. Goodfellow et al. reframed generation as a **game between two networks**. A generator maps
random noise to synthetic samples; a discriminator tries to tell real data from generated. They train
in opposition — the discriminator improves at spotting fakes, the generator improves at fooling it —
and at the game's equilibrium the generator's distribution matches the real data. Crucially this needs
*no explicit density*: the generator is trained purely by the gradient the discriminator provides, a
"likelihood-free" approach that yields strikingly sharp images. GANs dominated image synthesis for
years (DCGAN, BigGAN, StyleGAN's photoreal faces), but they are notoriously finicky — unstable
training and *mode collapse*, where the generator produces only a few varieties. Those pain points are
exactly what made the more stable, coverage-friendly diffusion models so attractive as successors.

## Key Ideas
- **Adversarial minimax game** — generator and discriminator optimise opposing objectives; the
  generator learns from the discriminator's gradient rather than a reconstruction target.
- **Implicit generative model** — no tractable likelihood is computed; sampling is a single forward
  pass through the generator, which is fast at inference.
- **Sharpness** — the adversarial signal pushes toward realistic, high-frequency detail, giving
  crisper images than likelihood-based methods of the era.
- **Instability and mode collapse** — training can oscillate or diverge, and the generator may cover
  only part of the data distribution; much follow-up work (WGAN, spectral norm) fought these issues.
