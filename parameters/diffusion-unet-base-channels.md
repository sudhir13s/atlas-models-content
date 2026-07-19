---
id: parameter/diffusion-unet-base-channels
type: parameter
slug: diffusion-unet-base-channels
schema_version: 1.0
title: "U-Net Base Channels (model_channels)"
name: model_channels
priority: 1
importance: critical
applies_to: [diffusion]
range: "128 → 320"
controls: "The base channel width every U-Net block is a multiple of. It is the master scaler of a diffusion model's size — nearly every block's cost grows with its square."
formula: "per-stage channels = model_channels × mult; block cost ∝ model_channels²"
if_increased: "More capacity to denoise fine detail and follow complex prompts, but parameters grow with the square of the base width — the dominant driver of model size."
if_decreased: "A smaller, faster U-Net, but a lower ceiling on image fidelity and prompt adherence."
tags: [diffusion, unet, channels, width, parameters, vision]
updated: 2026-07-19
sources:
  - { url: "https://arxiv.org/abs/2112.10752", title: "High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., 2022)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2006.11239", title: "Denoising Diffusion Probabilistic Models (Ho et al., 2020)", trust: 0.95 }
---

## Definition
`model_channels` is the **base channel width** of the denoising U-Net — the number every stage's
channel count is a multiple of. Combined with the per-stage channel multipliers, it sets how wide each
ResNet and attention block is, and therefore how much capacity the network has to predict noise.

It is the U-Net's master scaler: because ResNet and attention block costs grow with the **square** of
their channel width, `model_channels` is the single biggest lever on total parameter count.

## What to look for
The base width scales the whole model, so it is the primary size dial:

- **128:** smaller pixel-space DDPM-class models.
- **320:** Stable Diffusion 1.5's latent-space U-Net (~860M parameters).

Because cost grows quadratically, the **deep stages** (where the multiplier is 4×) carry the vast
majority of the weights — a `1280`-channel block is ~16× a `320`-channel one, so the model's size lives
in its low-resolution middle.

## Calculation
Nearly every block cost is quadratic in the stage width `C = model_channels × mult`. A ResNet block:

$$P_\text{res} \approx 2\,(3^2 C^2) + T\,C$$

and an attention block $\approx 4C^2$ (self) plus cross-attention and feed-forward terms, all in `C²`.
So doubling `model_channels` roughly **quadruples** the whole U-Net.

## Example
In Stable Diffusion 1.5, `model_channels = 320` with multipliers `[1, 2, 4, 4]` gives stage widths
`320 / 640 / 1280 / 1280`. One base-resolution ResNet block (`C = 320`) is ~2.25M parameters; the same
block at a `1280`-channel stage is ~16× larger. Summed across all stages the U-Net is ~860M — and
halving `model_channels` to 160 would cut it toward ~215M, dropping fidelity with it.
