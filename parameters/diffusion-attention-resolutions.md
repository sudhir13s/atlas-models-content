---
id: parameter/diffusion-attention-resolutions
type: parameter
slug: diffusion-attention-resolutions
schema_version: 3.1
title: Attention Resolutions
name: attention_resolutions
priority: 3
importance: important
applies_to:
- diffusion
range: coarse stages only (e.g. 8, 16, 32 downsample factors)
controls: At which resolution stages the U-Net inserts self- and cross-attention blocks. Attention is placed only at coarser stages, where it is affordable and where prompt conditioning enters.
formula: each inserted attention block adds ≈ 4C² (self) + cross-attention + feed-forward params at that stage
if_increased: Attention at more (finer) resolutions improves global coherence and prompt following, but each block adds C²-scale parameters and quadratic-in-tokens compute at high resolution.
if_decreased: Fewer attention stages shrink the model and speed sampling, but weaken long-range spatial consistency and text conditioning.
tags:
- diffusion
- unet
- attention
- cross-attention
- structure
- parameters
- vision
updated: '2026-07-19'
sources:
- url: https://arxiv.org/abs/2112.10752
  title: High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., 2022)
  trust: 0.95
- url: https://arxiv.org/abs/2006.11239
  title: Denoising Diffusion Probabilistic Models (Ho et al., 2020)
  trust: 0.95
---

## Definition

`attention_resolutions` lists which U-Net stages get **attention blocks** interleaved with their
ResNet blocks. Attention mixes information globally across the feature map (self-attention) and injects
the text prompt (cross-attention), but it is inserted only at the **coarser** resolutions.

The reason is cost: attention is quadratic in the number of spatial tokens, so it is affordable at low
resolution (few tokens) and prohibitive at high resolution — placement is a capacity-vs-cost decision.

## What to look for

Attention is deliberately kept out of the highest-resolution stages:

- **Coarse stages (8×, 16×, 32× downsampled):** attention blocks present — global structure and text
  conditioning happen here, cheaply.
- **Finest stage:** usually no attention — too many tokens, and local convolution suffices for detail.

Each attention block also carries the **cross-attention** to the text encoder (`context_dim = 768` in
SD 1.5), so these stages are where prompt adherence is built in.

## Calculation

Each inserted attention block adds real parameters at its stage width `C` (context dim `D`):

$$P_\text{attn} \approx \underbrace{4C^2}_{\text{self}} + \underbrace{2C^2 + 2DC}_{\text{cross}} + \underbrace{\sim 4C^2}_{\text{feed-forward}}$$

Adding a stage to the list adds these blocks at that stage's `C`; because deep stages have large `C`,
attention there is expensive despite the small token count. Removing a stage removes its attention
parameters entirely.

## Example

An SD 1.5 attention block at the base `C = 320`, `D = 768` stage is ~2.54M parameters (self ~0.41M,
cross ~0.70M, feed-forward ~1.23M, projections ~0.20M). At a `1280`-channel deep stage the `C²` terms
are ~16× larger. Enabling attention at one more resolution adds one such block per ResNet block in that
stage — a direct, sizeable addition on top of the convolutional cost.
