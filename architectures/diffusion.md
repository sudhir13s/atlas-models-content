---
id: architecture-era/diffusion
type: architecture-era
slug: diffusion
schema_version: 1.0
title: "Diffusion Models"
name: "Diffusion Model"
era: post-transformer
era_range: "2020–2026"
year: 2020
family: generation
core_innovation: "Learn to reverse a gradual noising process, generating data by iteratively denoising from pure noise."
description: "Iterative denoising generative models that displaced GANs for high-fidelity, diverse image synthesis."
key_features: ["Forward Gaussian noising schedule", "Learned iterative denoiser", "Stable likelihood-based training", "Classifier-free guidance", "Latent-space diffusion for efficiency"]
key_models: ["DDPM (2020)", "Stable Diffusion / LDM (2022)", "Imagen (2022)"]
paper_title: "Denoising Diffusion Probabilistic Models"
paper_url: "https://arxiv.org/abs/2006.11239"
influenced_by: [architecture-era/vae, architecture-era/gan]
led_to: []
tags: [diffusion, generation, denoising, text-to-image]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2006.11239", title: "Denoising Diffusion Probabilistic Models (Ho, Jain & Abbeel, 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2112.10752", title: "High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., 2022)", trust: 0.95 }
---

## What Changed
GANs made sharp images but were unstable and prone to mode collapse; VAEs were stable but blurry.
Diffusion models got both stability *and* fidelity by splitting generation into many easy steps. The
**forward process** takes a real image and adds Gaussian noise over many steps until it is
indistinguishable from static — a fixed, parameter-free schedule. A network is then trained to run the
**reverse process**: at each step, predict the noise (equivalently, a slightly denoised image), so
that starting from pure noise and denoising repeatedly reconstructs a sample from the data
distribution. Each step is a simple regression target, which makes training remarkably stable and the
model cover the full distribution. DDPM (Ho et al., 2020) established the recipe; **Latent Diffusion /
Stable Diffusion** (Rombach et al., 2022) made it practical by running the diffusion in a compressed
VAE latent space instead of on raw pixels, cutting compute enough for high-resolution text-to-image on
commodity GPUs. Diffusion descends from the latent-variable (VAE) and generative (GAN) lines and became
the dominant paradigm for image, audio, and video synthesis.

## Key Ideas
- **Forward noising** — a fixed schedule gradually corrupts data into Gaussian noise, defining the
  targets the model learns to reverse.
- **Learned denoising** — the network predicts the noise at each timestep; sampling chains many such
  predictions from noise back to a clean sample.
- **Stable training, full coverage** — per-step regression avoids adversarial instability and mode
  collapse, trading single-shot speed for many inference steps.
- **Latent diffusion + guidance** — diffusing in a VAE latent space slashes cost, and classifier-free
  guidance steers samples toward a text prompt, enabling controllable text-to-image generation.
