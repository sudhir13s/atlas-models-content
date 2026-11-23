---
id: parameter/diffusion-noise-schedule
type: parameter
slug: diffusion-noise-schedule
schema_version: 3.1
title: Noise Schedule (beta_schedule)
name: beta_schedule
priority: 4
importance: optional
applies_to:
- diffusion
range: linear | cosine | scaled-linear
controls: How the noise variance βt grows across the forward diffusion process. It defines how much noise is added at each timestep — a training/sampling design choice, not weights.
formula: 0 parameters — the schedule is a fixed sequence of βt values, not learnable weights
if_increased: A slower (e.g. cosine) schedule keeps more signal at high timesteps, often improving sample quality — but it changes training dynamics, not model size.
if_decreased: A more aggressive (steep linear) schedule destroys signal faster; it can simplify training but may hurt fidelity. Either way the parameter count is unchanged.
tags:
- diffusion
- noise-schedule
- beta
- training
- vision
updated: '2026-07-19'
sources:
- url: https://arxiv.org/abs/2006.11239
  title: Denoising Diffusion Probabilistic Models (Ho et al., 2020)
  trust: 0.95
- url: https://arxiv.org/abs/2102.09672
  title: Improved Denoising Diffusion Probabilistic Models (Nichol & Dhariwal, 2021)
  trust: 0.9
---

## Definition

The noise schedule is the sequence of variances `βt` that defines the **forward diffusion process** —
how much Gaussian noise is added to the image at each timestep `t`, from clean data at `t = 0` to pure
noise at `t = T`. Common forms are **linear** (DDPM) and **cosine** (an improvement for high timesteps).

It is a **hyperparameter of the process, not the network: it carries zero parameters.** The U-Net's
weights are identical regardless of the schedule; only the training targets and sampling trajectory
change.

## What to look for

The schedule shapes how signal is destroyed over time, which matters most near the endpoints:

- **Linear:** simple, but destroys almost all signal well before `t = T`, wasting late timesteps.
- **Cosine:** adds noise more gradually, keeping useful signal at high `t` — measurably better samples
  and log-likelihood (Nichol & Dhariwal).

This is a **training/sampling** knob: it interacts with the number of timesteps and the sampler, not
with model capacity.

## Calculation

The schedule contributes **no parameters**. It is a fixed length-`T` array of `βt` (or an `αt` /
`ᾱt` derivation), used to form the closed-form forward corruption:

$$q(x_t \mid x_0) = \mathcal{N}\!\big(x_t;\ \sqrt{\bar\alpha_t}\,x_0,\ (1-\bar\alpha_t)\mathbf{I}\big), \qquad \bar\alpha_t = \prod_{s=1}^{t}(1-\beta_s)$$

Because `βt` values are constants, changing the schedule changes what the U-Net is trained to predict,
never how many weights it has.

## Example

DDPM used a linear schedule with `β` rising from `1e-4` to `0.02` over `T = 1000` steps. Nichol &
Dhariwal replaced it with a cosine schedule and improved sample quality — with **no change to the
U-Net's ~parameter count**, because the schedule lives entirely in the data corruption, not the model.
This is why it sits among the architectural, zero-parameter knobs despite its impact on quality.
