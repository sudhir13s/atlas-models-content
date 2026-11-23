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
pipeline:
  source: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/stable_diffusion/pipeline_stable_diffusion.py
  summary: The image is made in a small latent space, not in pixels. Random noise is cleaned step by step by a U-Net that reads the prompt, and a decoder turns the result into the picture.
  side_into: unet
  stages:
  - id: prompt
    role: input
    title: Text prompt
    side: true
  - id: tokenizer
    role: tokenizer
    title: CLIP tokenizer
    side: true
  - id: text-encoder
    role: textEncoder
    title: CLIP text encoder
    note: Turns the prompt into a sequence of vectors. The U-Net reads them at every step of the loop.
    side: true
  - id: noise
    role: input
    title: Random noise
    details:
    - in the latent space
    note: Generation starts from random numbers in the latent space of the VAE, not from pixels.
  - id: unet
    role: denoiser
    title: U-Net
    details:
    - predicts the noise in the latents
    note: Reads the noisy latents, the step and the prompt vectors, and predicts the noise in the latents. With guidance on, it runs with and without the prompt and the two predictions are combined.
    frame: Denoising loop
  - id: scheduler
    role: step
    title: Scheduler step
    details:
    - PNDM · removes predicted noise
    note: Uses the predicted noise to make the latents a little cleaner. The loop runs once per inference step; the pipeline's default is 50 steps.
    frame: Denoising loop
  - id: vae
    role: autoencoder
    title: VAE decoder
    details:
    - latents to pixels
    note: The decoder half of the autoencoder turns the cleaned latents into an image.
  - id: safety
    role: step
    title: Safety checker
    note: A separate classifier that checks the finished image; the repository ships it as part of the pipeline.
  - id: image
    role: outputHead
    title: Image
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
- url: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/stable_diffusion/pipeline_stable_diffusion.py
  title: 'diffusers/pipelines/stable_diffusion/pipeline_stable_diffusion.py: reference pipeline (Hugging Face Diffusers)'
  trust: 0.9
- url: https://huggingface.co/CompVis/stable-diffusion-v1-4/blob/main/model_index.json
  title: CompVis/stable-diffusion-v1-4 — model_index.json
  trust: 0.9
---

## Summary

Stable Diffusion v1-4 is a text-to-image model: it generates an image from a written prompt and can also modify images. The card calls it a latent diffusion model, one that works on a compressed form of the image, using a fixed pretrained CLIP text encoder. This checkpoint started from the Stable-Diffusion-v1-2 weights and was fine-tuned for 225k steps at 512x512 resolution on "laion-aesthetics v2 5+". The weights are published under the CompVis account for use with the Diffusers library.

## Intended Use

The card says the model is intended for research purposes only. Its research areas are safe deployment of models that could generate harmful content, understanding the limits and biases of generative models, artworks and design, educational or creative tools, and research on generative models. It lists as out of scope any use that presents the images as factual or true representations of people or events, and any misuse such as demeaning content, impersonation or disinformation.

## What Sets It Apart

The card describes a latent diffusion model with a fixed, pretrained text encoder, CLIP ViT-L/14, as suggested in the Imagen paper. For this version it says 10% of the text conditioning was dropped during fine-tuning to improve classifier-free guidance sampling.

## Limitations

The card says the model does not achieve perfect photorealism, cannot render legible text, struggles with compositional prompts such as a red cube on top of a blue sphere, and may not generate faces and people properly. It was trained mainly on English captions. Its training data, LAION-5B, contains adult material, so the card says the model is not fit for product use without additional safety mechanisms.
