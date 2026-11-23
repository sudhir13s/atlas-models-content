---
id: parameter-calculator/diffusion
type: parameter-calculator
slug: diffusion
schema_version: 3.1
title: Diffusion Model (U-Net) Parameters
arch: diffusion
order: 7
color_key: diffusion
components:
- name: base channel width (model_channels)
  rank: 1
  importance: critical
  badge: Structure
  formula: scales every ResNet and attention block; cost ∝ model_channels²
  range: 128 → 320
- name: channel multipliers (resolution stages)
  rank: 2
  importance: critical
  badge: Structure
  formula: per-stage channels = model_channels × mult
  range: '[1, 2, 4, 4]'
- name: ResNet blocks (conv + groupnorm + time-embed proj)
  rank: 3
  importance: critical
  badge: Per Block
  formula: ≈ 2 × (3² × C²) + time_dim × C; the bulk of params
  range: num_res_blocks per stage
- name: self-attention blocks (spatial transformer)
  rank: 4
  importance: critical
  badge: Per Block
  formula: Q,K,V,O each C × C = 4·C² per attention
  range: at coarser stages
- name: cross-attention (text conditioning)
  rank: 5
  importance: critical
  badge: Per Block
  formula: Q = C×C; K,V = context_dim×C; O = C×C
  range: context_dim = 768
- name: down blocks (encoder) / up blocks (decoder)
  rank: 6
  importance: critical
  badge: Structure
  formula: symmetric U; up blocks take skip-concat inputs (2×C_in)
  range: 4 stages each
- name: time-step embedding (sinusoidal → MLP)
  rank: 7
  importance: important
  badge: Per Model
  formula: '2 linear layers: model_channels → time_dim → time_dim'
  range: time_dim = 4·model_channels
- name: num_res_blocks per stage
  rank: 8
  importance: important
  badge: Structure
  formula: multiplies ResNet-block cost at each resolution
  range: 2 → 3
- name: attention head count / head_dim
  rank: 9
  importance: important
  badge: Per Block
  formula: splits C into heads; no extra params, sets attention pattern
  range: 8 heads typical
- name: in / out conv (latent ↔ model_channels)
  rank: 10
  importance: optional
  badge: Head
  formula: 3² × in_ch × model_channels (in) and × out_ch (out)
  range: latent = 4 ch
- name: group normalization (γ, β)
  rank: 11
  importance: optional
  badge: Per Block
  formula: 2 × channels per GroupNorm
  range: 2·C
- name: transformer depth per attention block
  rank: 12
  importance: optional
  badge: Per Block
  formula: stacks self+cross+FF sub-blocks (usually depth 1)
  range: 1 → 2
worked_example:
  label: Stable Diffusion 1.5 U-Net (~860M, approximate)
  config:
    modelChannels: 320
    channelMult: '[1, 2, 4, 4]'
    stageChannels: 320 / 640 / 1280 / 1280
    numResBlocks: 2
    contextDim: 768
    timeDim: 1280
    latentChannels: 4
    attentionResolutions: 8,16,32 downsample
    totalParams: ~860M
  sources:
  - url: https://arxiv.org/abs/2112.10752
    title: High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., 2022)
    trust: 0.95
tags:
- diffusion
- unet
- generative
- attention
- parameters
- vision
updated: '2026-07-17'
sources:
- url: https://arxiv.org/abs/2006.11239
  title: Denoising Diffusion Probabilistic Models (Ho et al., 2020)
  trust: 0.95
- url: https://arxiv.org/abs/2112.10752
  title: High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., 2022)
  trust: 0.95
- url: https://arxiv.org/abs/1505.04597
  title: 'U-Net: Convolutional Networks for Biomedical Image Segmentation (Ronneberger et al., 2015)'
  trust: 0.9
---

## Overview

A diffusion model learns to denoise. The trainable weights all live in a single **U-Net** that,
given a noisy image (or latent) and a timestep, predicts the noise to remove. The U-Net is a
symmetric encoder–decoder: a **down path** that halves spatial resolution while doubling
channels, a **bottleneck**, and an **up path** that mirrors it, with skip connections carrying
encoder features across to the decoder. Its parameters are dominated by stacks of **ResNet
blocks** (two 3×3 convolutions plus a timestep-conditioning projection) interleaved with
**attention blocks** at the coarser resolutions.

Text-to-image diffusion adds **cross-attention**: the query comes from image features, but the
keys and values come from a frozen text encoder's embeddings (CLIP, `context_dim = 768` in
Stable Diffusion). This is the only place the text conditioning enters, and it is what makes the
same U-Net "listen" to a prompt. The **timestep** is embedded (sinusoidal → small MLP) and added
into every ResNet block, so the network knows how much noise it is dealing with.

## Components

- **Base channel width (model_channels)** is the master scaler — nearly every block's cost grows
  with its square. Channel multipliers `[1,2,4,4]` set per-stage width (`320/640/1280/1280` in
  SD 1.5).
- **ResNet blocks** hold most of the parameters: two `3×3` convs at the stage's channel count
  plus a linear projection from the timestep embedding into the block.
- **Self-attention** mixes information spatially at coarse resolutions (`Q,K,V,O`, each `C×C`).
  **Cross-attention** injects text: `Q` is `C×C`, but `K,V` map `context_dim → C`.
- **Down/up symmetry**: up blocks receive the concatenated skip connection, so their first conv
  sees `2·C_in` — decoder blocks are slightly larger than their encoder twins.
- **Timestep embedding** is tiny but global; **group norms** and **in/out convs** (latent
  `4 → model_channels` and back) round out the count.

Per-layer formulas. A ResNet block at channel width `C` with timestep dim `T`:

$$P_\text{res} \approx 2\,(3^2 C^2) + T\,C \;+\; \text{group-norm } (4C)$$

A spatial-transformer attention block (self + cross + feed-forward) at width `C`, context `D`:

$$P_\text{attn} \approx \underbrace{4C^2}_{\text{self}} + \underbrace{2C^2 + 2DC}_{\text{cross}} + \underbrace{\sim 4C^2}_{\text{GEGLU FF}}$$

Full model = sum over all down, bottleneck and up blocks — large and irregular, so the total is
best read from the published figure and cross-checked block-by-block.

## Worked Example

Stable Diffusion 1.5 U-Net (`config` above): `model_channels = 320`, multipliers `[1,2,4,4]`
(stage widths `320/640/1280/1280`), `num_res_blocks = 2`, `context_dim = 768`, `time_dim =
1280`, latent `4` channels. Two representative blocks summed explicitly:

- **One base-resolution ResNet block** (`C = 320`): two convs $2 \times (3^2 \times 320^2) =
  2 \times 921\,600 = 1\,843\,200$; timestep projection $1280 \times 320 = 409\,600$; group
  norms $\approx 1\,280$ → **≈ 2.25M**.
- **One base-resolution attention block** (`C = 320`, `D = 768`): self-attn $4 \times 320^2 =
  409\,600$; cross-attn $320^2 + 2(768 \times 320) + 320^2 = 102\,400 + 491\,520 + 102\,400 =
  696\,320$; GEGLU feed-forward $\approx 1.23\text{M}$; `1×1` proj-in/out $\approx 0.20\text{M}$
  → **≈ 2.54M**.

Costs scale with `C²`, so the same blocks at the `1280`-channel stages are ~16× larger — which
is why the deep stages, not the shallow ones, hold the bulk of the weights. Summing all four
down stages, the bottleneck, and the four (skip-widened) up stages gives the published **≈ 860M
parameters** for the SD 1.5 U-Net. (The VAE and CLIP text encoder are separate frozen models,
not part of this count.) The per-block figures above are exact; the full-model total is the
published figure, since the irregular stage structure makes a hand sum error-prone — treat the
860M as the citable anchor and the block math as the intuition for where it comes from.
