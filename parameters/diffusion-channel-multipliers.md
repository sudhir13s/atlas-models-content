---
id: parameter/diffusion-channel-multipliers
type: parameter
slug: diffusion-channel-multipliers
schema_version: 3.1
title: Channel Multipliers (Resolution Stages)
name: channel_mult
priority: 2
importance: critical
applies_to:
- diffusion
range: '[1, 2, 4, 4] (typical)'
controls: The per-stage multipliers applied to the base width as the U-Net goes deeper. They set how quickly channels grow while spatial resolution shrinks.
formula: stage channels = model_channels × mult[stage]; block cost ∝ (model_channels × mult)²
if_increased: Larger multipliers make the deep, low-resolution stages much wider — big capacity gains, but a quadratic blow-up in parameters at those stages.
if_decreased: Smaller multipliers keep deep stages narrow, shrinking the model, but reduce the abstract capacity where the U-Net does its heaviest reasoning.
tags:
- diffusion
- unet
- channels
- structure
- parameters
- vision
updated: '2026-07-19'
sources:
- url: https://arxiv.org/abs/2112.10752
  title: High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., 2022)
  trust: 0.95
- url: https://arxiv.org/abs/1505.04597
  title: 'U-Net: Convolutional Networks for Biomedical Image Segmentation (Ronneberger et al., 2015)'
  trust: 0.9
---

## Definition

Channel multipliers are the list of factors applied to `model_channels` at each **resolution stage** of
the U-Net. A schedule like `[1, 2, 4, 4]` means the four encoder stages have widths `1× / 2× / 4× / 4×`
the base — channels grow as spatial resolution halves, so the network gets wider as it goes deeper.

They shape *where* the parameters sit. Together with the base width they set every stage's channel
count, and thus (quadratically) every block's size.

## What to look for

The multiplier schedule encodes the classic U-Net trade of resolution for width:

- Early stages (`1×`, `2×`): high spatial resolution, few channels — cheap per block, many of them.
- Deep stages (`4×`): low resolution, many channels — expensive per block, and where **attention** and
  most parameters live.

A schedule that plateaus (`…, 4, 4`) caps the deepest width to control the quadratic cost, rather than
letting it keep doubling.

## Calculation

Each stage's block cost scales with the **square** of its multiplied width:

$$C_\text{stage} = \text{model\_channels} \times \text{mult}[\text{stage}], \qquad P_\text{block} \propto C_\text{stage}^2$$

So a `4×` stage costs `16×` a `1×` stage per block. The multipliers therefore concentrate the model's
parameters in the deepest stages — the reason those stages dominate the total.

## Example

Stable Diffusion 1.5 uses `model_channels = 320` with `[1, 2, 4, 4]`, giving widths `320 / 640 / 1280 /
1280`. A ResNet block at the `320` stage is ~2.25M parameters; at the `1280` stage the same block is
$\left(\tfrac{1280}{320}\right)^2 = 16\times$ larger, ~36M. The two deep `4×` stages, not the shallow
ones, hold the bulk of the U-Net's ~860M parameters.
