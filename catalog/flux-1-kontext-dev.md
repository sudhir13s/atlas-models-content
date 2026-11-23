---
id: model/flux-1-kontext-dev
type: model
slug: flux-1-kontext-dev
schema_version: 1.1
title: FLUX.1-Kontext-dev
organization: organization/black-forest-labs
release_date: '2025-05-28'
release_year: 2025
release_month: 5
license_note: flux-1-dev-non-commercial-license
model_category: generation
tasks:
- task/image-editing
total_parameters: 11901408320
summary: FLUX.1 Kontext [dev] is an image-editing model published by Black Forest Labs. You give it an existing image and a text instruction, and it returns the edited image. The card describes it as a 12 billion parameter rectified flow transformer, a type of image-generation network, released with open weights.
what_sets_it_apart: 'The card lists these features: it changes an existing image from an edit instruction, it keeps a character, style or object as a reference without any fine-tuning, and it stays consistent over several successive edits with minimal visual drift. It says the model was trained with guidance distillation, which makes it more efficient.'
running_it: The card says the repository includes filters for illegal or infringing content, and that filters or manual review must be used with the model under the terms of the FLUX.1 [dev] Non-Commercial License. It points to a reference implementation with sampling code on GitHub and to use through the diffusers library.
pipeline:
  source: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/flux/pipeline_flux_kontext.py
  summary: The input image is turned into latent tokens and placed after the tokens of the image being made, so the transformer sees both in one sequence. A CLIP and a T5 text encoder read the instruction, random noise is cleaned step by step, and a decoder turns the result into the edited picture.
  side_into: transformer
  stages:
  - id: prompt
    role: input
    title: Text instruction
    side: true
  - id: tokenizer
    role: tokenizer
    title: CLIP + T5 tokenizers
    details:
    - two
    side: true
  - id: text-encoder
    role: textEncoder
    title: CLIP + T5 text encoders
    details:
    - CLIP · one pooled vector
    - T5 · one vector per token
    note: The two encoders read the instruction side by side. T5 gives one vector per token, which the transformer reads as text tokens; CLIP gives one pooled vector, which conditions every block together with the step.
    side: true
  - id: image
    role: input
    title: Input image
    details:
    - resized to a trained resolution
  - id: vae-encoder
    role: autoencoder
    title: VAE encoder
    details:
    - image to latents
    note: The frozen encoder half of the FLUX autoencoder turns the input image into latents.
  - id: pack
    role: step
    title: Pack into tokens
    details:
    - 2 × 2 latent patches per token
    note: Each 2 by 2 patch of the latents becomes one context token. Its position for the rotary embedding is marked with a separate index, so the model can tell context tokens from the tokens being made.
  - id: join
    role: projector
    title: Join with noisy tokens
    details:
    - appended in the sequence
    note: The image being made starts as random noise in the latent space. At every step the context tokens are appended after its tokens, and both enter the image stream of the transformer.
    frame: Denoising loop
  - id: transformer
    role: denoiser
    title: Flux transformer
    details:
    - double-stream blocks
    - then 38 single-stream blocks
    note: Double-stream blocks keep separate weights for image and text tokens and mix them in one attention; the sequences are then joined for 38 single-stream blocks. Only the outputs at the positions of the image being made are kept.
    frame: Denoising loop
  - id: scheduler
    role: step
    title: Scheduler step
    details:
    - flow matching · Euler
    note: Moves the latents one step towards a clean image using the transformer's prediction. The loop runs once per inference step; the pipeline's default is 28 steps.
    frame: Denoising loop
  - id: vae
    role: autoencoder
    title: VAE decoder
    details:
    - latents to pixels
    note: The tokens are unpacked back into a latent image, and the decoder half of the autoencoder turns it into pixels.
  - id: output
    role: outputHead
    title: Edited image
filters:
  size_category: medium
  is_gated: true
  pipeline_tag: image-to-image
  library: diffusers
hub_signals:
  downloads_30d: 165515
  likes: 2976
  last_modified: '2026-11-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/black-forest-labs/FLUX.1-Kontext-dev
aliases:
- black-forest-labs/FLUX.1-Kontext-dev
lineage:
  parents: []
  children: []
quotes:
  summary: is a 12 billion parameter rectified flow transformer capable of editing images based on text instructions.
  what_sets_it_apart: Robust consistency allows users to refine an image through multiple successive edits with minimal visual drift.
  running_it: Filters or manual review must be used with the model under the terms of the FLUX.1 [dev] Non-Commercial License.
tags:
- image-to-image
updated: '2026-11-23'
sources:
- url: https://huggingface.co/black-forest-labs/FLUX.1-Kontext-dev
  title: black-forest-labs/FLUX.1-Kontext-dev — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2506.15742
  title: arXiv:2506.15742
  trust: 0.95
- url: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/flux/pipeline_flux_kontext.py
  title: 'diffusers/pipelines/flux/pipeline_flux_kontext.py: reference pipeline (Hugging Face Diffusers)'
  trust: 0.9
---

## Summary

FLUX.1 Kontext [dev] is an image-editing model published by Black Forest Labs. You give it an existing image and a text instruction, and it returns the edited image. The card describes it as a 12 billion parameter rectified flow transformer, a type of image-generation network, released with open weights.

## What Sets It Apart

The card lists these features: it changes an existing image from an edit instruction, it keeps a character, style or object as a reference without any fine-tuning, and it stays consistent over several successive edits with minimal visual drift. It says the model was trained with guidance distillation, which makes it more efficient.

## Running It

The card says the repository includes filters for illegal or infringing content, and that filters or manual review must be used with the model under the terms of the FLUX.1 [dev] Non-Commercial License. It points to a reference implementation with sampling code on GitHub and to use through the diffusers library.
