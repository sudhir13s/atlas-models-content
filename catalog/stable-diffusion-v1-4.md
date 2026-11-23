---
id: model/stable-diffusion-v1-4
type: model
slug: stable-diffusion-v1-4
schema_version: 1.1
title: stable-diffusion-v1-4
organization: organization/compvis
release_date: '2022-08-20'
release_year: 2022
release_month: 8
license_note: creativeml-openrail-m
model_category: generation
tasks:
- task/image-generation
total_parameters: 859520964
summary: 'Stable Diffusion v1-4 is a text-to-image model: it generates an image from a written prompt and can also modify images. The card calls it a latent diffusion model, one that works on a compressed form of the image, using a fixed pretrained CLIP text encoder. This checkpoint started from the Stable-Diffusion-v1-2 weights and was fine-tuned for 225k steps at 512x512 resolution on "laion-aesthetics v2 5+". The weights are published under the CompVis account for use with the Diffusers library.'
intended_use: The card says the model is intended for research purposes only. Its research areas are safe deployment of models that could generate harmful content, understanding the limits and biases of generative models, artworks and design, educational or creative tools, and research on generative models. It lists as out of scope any use that presents the images as factual or true representations of people or events, and any misuse such as demeaning content, impersonation or disinformation.
what_sets_it_apart: The card describes a latent diffusion model with a fixed, pretrained text encoder, CLIP ViT-L/14, as suggested in the Imagen paper. For this version it says 10% of the text conditioning was dropped during fine-tuning to improve classifier-free guidance sampling.
limitations: The card says the model does not achieve perfect photorealism, cannot render legible text, struggles with compositional prompts such as a red cube on top of a blue sphere, and may not generate faces and people properly. It was trained mainly on English captions. Its training data, LAION-5B, contains adult material, so the card says the model is not fit for product use without additional safety mechanisms.
filters:
  size_category: tiny
  is_gated: false
  pipeline_tag: text-to-image
  library: diffusers
hub_signals:
  downloads_30d: 450196
  likes: 7150
  last_modified: '2023-08-23'
  observed_at: '2026-11-21'
  source: https://huggingface.co/CompVis/stable-diffusion-v1-4
aliases:
- CompVis/stable-diffusion-v1-4
lineage:
  parents: []
  children: []
quotes:
  summary: fine-tuned on 225k steps at resolution 512x512
  intended_use: The model is intended for research purposes only.
  what_sets_it_apart: 10% dropping of the text-conditioning to improve
  limitations: The model cannot render legible text
tags:
- text-to-image
updated: '2026-11-23'
sources:
- url: https://huggingface.co/CompVis/stable-diffusion-v1-4
  title: CompVis/stable-diffusion-v1-4 — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2207.12598
  title: arXiv:2207.12598
  trust: 0.95
- url: https://arxiv.org/abs/2112.10752
  title: arXiv:2112.10752
  trust: 0.95
- url: https://arxiv.org/abs/2103.00020
  title: arXiv:2103.00020
  trust: 0.95
- url: https://arxiv.org/abs/2205.11487
  title: arXiv:2205.11487
  trust: 0.95
- url: https://arxiv.org/abs/1910.09700
  title: arXiv:1910.09700
  trust: 0.95
---

## Summary

Stable Diffusion v1-4 is a text-to-image model: it generates an image from a written prompt and can also modify images. The card calls it a latent diffusion model, one that works on a compressed form of the image, using a fixed pretrained CLIP text encoder. This checkpoint started from the Stable-Diffusion-v1-2 weights and was fine-tuned for 225k steps at 512x512 resolution on "laion-aesthetics v2 5+". The weights are published under the CompVis account for use with the Diffusers library.

## Intended Use

The card says the model is intended for research purposes only. Its research areas are safe deployment of models that could generate harmful content, understanding the limits and biases of generative models, artworks and design, educational or creative tools, and research on generative models. It lists as out of scope any use that presents the images as factual or true representations of people or events, and any misuse such as demeaning content, impersonation or disinformation.

## What Sets It Apart

The card describes a latent diffusion model with a fixed, pretrained text encoder, CLIP ViT-L/14, as suggested in the Imagen paper. For this version it says 10% of the text conditioning was dropped during fine-tuning to improve classifier-free guidance sampling.

## Limitations

The card says the model does not achieve perfect photorealism, cannot render legible text, struggles with compositional prompts such as a red cube on top of a blue sphere, and may not generate faces and people properly. It was trained mainly on English captions. Its training data, LAION-5B, contains adult material, so the card says the model is not fit for product use without additional safety mechanisms.
