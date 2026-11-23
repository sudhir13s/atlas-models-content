---
id: model/stable-diffusion-xl-base-1-0
type: model
slug: stable-diffusion-xl-base-1-0
schema_version: 1.1
title: stable-diffusion-xl-base-1.0
organization: organization/stabilityai
release_date: '2023-07-25'
release_year: 2023
release_month: 7
license_note: openrail++
model_category: generation
tasks:
- task/image-generation
total_parameters: 2567463684
summary: 'Stable Diffusion XL (SDXL) 1.0 base is a text-to-image model from Stability AI: it turns a written prompt into an image. The card calls it a latent diffusion model, a design that builds an image by repeatedly removing noise from a compressed representation, and says it uses two fixed, pretrained text encoders, OpenCLIP-ViT/G and CLIP-ViT/L.'
intended_use: The card says the model is intended for research purposes only, such as artworks and design processes, educational or creative tools, research on generative models, and studying limitations and biases. It says it was not trained to be factual representations of people or events, so using it for that is out of scope.
what_sets_it_apart: 'The card describes an ensemble-of-experts pipeline: the base model generates noisy latents, and a separate refinement model specialised for the final denoising steps can then process them. The base model can also be used by itself.'
running_it: The card says to use diffusers 0.19.0 or later, together with transformers, safetensors, accelerate and the invisible watermark package.
filters:
  size_category: small
  is_gated: false
  pipeline_tag: text-to-image
  library: diffusers
hub_signals:
  downloads_30d: 4786154
  likes: 8491
  last_modified: '2023-10-30'
  observed_at: '2026-11-21'
  source: https://huggingface.co/stabilityai/stable-diffusion-xl-base-1.0
aliases:
- stabilityai/stable-diffusion-xl-base-1.0
lineage:
  parents: []
  children: []
quotes:
  summary: Diffusion-based text-to-image generative model
  intended_use: The model is intended for research purposes only.
  what_sets_it_apart: In a first step, the base model is used to generate (noisy) latents,
  running_it: 'Make sure to upgrade diffusers to >= 0.19.0:'
tags:
- text-to-image
updated: '2026-11-23'
sources:
- url: https://huggingface.co/stabilityai/stable-diffusion-xl-base-1.0
  title: stabilityai/stable-diffusion-xl-base-1.0 — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2307.01952
  title: arXiv:2307.01952
  trust: 0.95
- url: https://arxiv.org/abs/2211.01324
  title: arXiv:2211.01324
  trust: 0.95
- url: https://arxiv.org/abs/2108.01073
  title: arXiv:2108.01073
  trust: 0.95
- url: https://arxiv.org/abs/2112.10752
  title: arXiv:2112.10752
  trust: 0.95
---

## Summary

Stable Diffusion XL (SDXL) 1.0 base is a text-to-image model from Stability AI: it turns a written prompt into an image. The card calls it a latent diffusion model, a design that builds an image by repeatedly removing noise from a compressed representation, and says it uses two fixed, pretrained text encoders, OpenCLIP-ViT/G and CLIP-ViT/L.

## Intended Use

The card says the model is intended for research purposes only, such as artworks and design processes, educational or creative tools, research on generative models, and studying limitations and biases. It says it was not trained to be factual representations of people or events, so using it for that is out of scope.

## What Sets It Apart

The card describes an ensemble-of-experts pipeline: the base model generates noisy latents, and a separate refinement model specialised for the final denoising steps can then process them. The base model can also be used by itself.

## Running It

The card says to use diffusers 0.19.0 or later, together with transformers, safetensors, accelerate and the invisible watermark package.
