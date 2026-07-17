---
id: architecture-era/vae
type: architecture-era
slug: vae
schema_version: 1.0
title: "Variational Autoencoder"
name: "Variational Autoencoder"
era: foundation
era_range: "2013–2020"
year: 2013
family: generation
core_innovation: "A probabilistic encoder-decoder trained by variational inference with the reparameterization trick, learning a continuous latent space."
description: "The likelihood-based latent-variable generative model that made deep variational inference practical."
key_features: ["Probabilistic encoder (approximate posterior)", "Continuous latent space", "Evidence lower bound (ELBO) objective", "Reparameterization trick", "KL-regularized latents"]
key_models: ["VAE (2013)", "VQ-VAE (2017)", "NVAE (2020)"]
paper_title: "Auto-Encoding Variational Bayes"
paper_url: "https://arxiv.org/abs/1312.6114"
influenced_by: []
led_to: [architecture-era/diffusion]
tags: [vae, generation, latent-variable, variational-inference]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1312.6114", title: "Auto-Encoding Variational Bayes (Kingma & Welling, 2013)", trust: 0.95 }
---

## What Changed
Kingma and Welling gave deep learning a principled, likelihood-based generative model. A VAE pairs an
**encoder** that maps a data point to a *distribution* over latent codes with a **decoder** that
reconstructs data from a sampled code. Training maximises the **evidence lower bound (ELBO)**, which
balances two terms: reconstruction quality, and a KL penalty pulling the encoder's posterior toward a
simple prior (usually a standard Gaussian). The technical unlock is the **reparameterization trick** —
writing a sampled latent as $z = \mu + \sigma \odot \epsilon$ with $\epsilon \sim \mathcal{N}(0, I)$ —
which moves the randomness outside the network so gradients can flow through the sampling step and the
whole thing trains by ordinary backprop. The payoff is a smooth, continuous latent space you can
sample and interpolate. VAEs trade some sample sharpness for stable training and good distribution
coverage — the opposite failure profile to GANs — and their latent-variable, likelihood view directly
underlies diffusion models, which can be seen as deep hierarchical latent-variable models.

## Key Ideas
- **Probabilistic encoder** — outputs a posterior $q(z\mid x)$ rather than a point, so nearby inputs
  map to overlapping latent regions and the space stays continuous.
- **ELBO objective** — maximise a tractable lower bound on data likelihood: reconstruction term minus
  a KL term regularising the latent toward the prior.
- **Reparameterization trick** — factor sampling into a deterministic function of the parameters plus
  external noise, enabling gradient-based training through a stochastic node.
- **Coverage over sharpness** — VAE samples are often blurrier than GANs' but the model rarely
  collapses and covers the full data distribution, aiding controllable generation.
