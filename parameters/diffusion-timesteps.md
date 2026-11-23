---
id: parameter/diffusion-timesteps
type: parameter
slug: diffusion-timesteps
schema_version: 3.1
title: Diffusion Timesteps (T)
name: num_train_timesteps
priority: 5
importance: optional
applies_to:
- diffusion
range: 1000 (training) → 20–50 (sampling with fast solvers)
controls: How many denoising steps the diffusion process is discretized into. It sets the granularity of the noise trajectory — a process hyperparameter, not model weights.
formula: 0 parameters — T sets the number of steps; the timestep is embedded via a fixed sinusoidal → MLP whose size depends on time_dim, not T
if_increased: More steps give a finer, higher-quality denoising trajectory, but linearly more compute per generated image — with no change to parameter count.
if_decreased: Fewer steps make sampling much faster (fast solvers reach ~20–50), at some quality cost; the model itself is unchanged.
tags:
- diffusion
- timesteps
- sampling
- training
- vision
updated: '2026-07-19'
sources:
- url: https://arxiv.org/abs/2006.11239
  title: Denoising Diffusion Probabilistic Models (Ho et al., 2020)
  trust: 0.95
- url: https://arxiv.org/abs/2010.02502
  title: Denoising Diffusion Implicit Models (DDIM, Song et al., 2020)
  trust: 0.9
---

## Definition

`num_train_timesteps` (`T`) is how many steps the diffusion process is broken into — the number of
noise levels between a clean image (`t = 0`) and pure noise (`t = T`). Training samples a random `t`
each step; sampling walks the trajectory from noise back to an image.

`T` is a **process hyperparameter with zero parameters.** The timestep is fed to the U-Net through a
fixed **sinusoidal embedding → small MLP**, whose size depends on `time_dim`, **not** on `T` — so
changing `T` never changes the weight count.

## What to look for

There are two distinct timestep counts, and conflating them is a common mistake:

- **Training `T` (≈1000):** the granularity the model learns to denoise over.
- **Sampling steps (≈20–50):** fast solvers (DDIM, DPM-Solver) skip steps at inference, generating
  good images in far fewer than `T` passes.

More steps → smoother trajectory and better quality → **linearly more compute per image**, but never
more parameters.

## Calculation

`T` contributes **no parameters**. The timestep embedding is a fixed transform followed by a small MLP
whose cost is set by `time_dim`:

$$P_\text{time-embed} = \text{model\_channels} \times \text{time\_dim} + \text{time\_dim} \times \text{time\_dim}$$

Note the absence of `T` in this expression — a 1000-step and a 100-step model have identical embedding
weights. `T` only multiplies the number of forward passes at sampling time.

## Example

Stable Diffusion and DDPM train with `T = 1000` but generate with ~20–50 sampling steps via DDIM,
cutting inference cost by ~20–50× with a small quality trade — and **no change to the U-Net's ~860M
parameters**. The timestep MLP is sized by `time_dim = 1280`, independent of whether `T` is 1000 or
100, which is exactly why timesteps sit among the zero-parameter, architectural knobs.
