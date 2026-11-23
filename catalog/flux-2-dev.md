---
id: model/flux-2-dev
type: model
slug: flux-2-dev
schema_version: 3.0
title: FLUX.2-dev
organization: organization/black-forest-labs
release_date: '2025-11-22'
license_note: flux-non-commercial-license
model_category: generation
model_kind: diffusion
tasks:
- task/image-editing
total_parameters: 32223281152
summary: FLUX.2 [dev] is an image model from Black Forest Labs that makes and edits pictures from written instructions. The card describes it as a 32 billion parameter rectified flow transformer that generates, edits and combines images based on text instructions. Its weights are open, but access on Hugging Face requires accepting the FLUX Non-Commercial License Agreement.
intended_use: The card lists open text-to-image generation, single-reference editing and multi-reference editing. It says the open weights are meant to drive new scientific research and to help artists build new workflows, and that generated outputs can be used for personal, scientific and commercial purposes as described in the license. For local use on a consumer graphics card such as an RTX 4090, the card points to a 4-bit quantized version in the diffusers library.
what_sets_it_apart: The card says one model can use a character, object or style reference without any fine-tuning, so no extra training is needed per subject. It also says the model was trained using guidance distillation, a training technique, which the card says makes it more efficient.
pipeline:
  source: https://github.com/black-forest-labs/flux2/blob/main/src/flux2/model.py
  summary: The image is made in the latent space of an autoencoder, not in pixels. A Mistral Small 3.2 language model reads the prompt, a transformer cleans random noise step by step while reading its output, and a decoder turns the result into the picture. Reference images, when given, are encoded into latent tokens and added to the same sequence.
  side_into: transformer
  stages:
  - id: prompt
    role: input
    title: Text prompt
    details:
    - inside a chat template
    side: true
  - id: text-encoder
    role: textEncoder
    title: Mistral Small 3.2 encoder
    details:
    - 24B · layers 10, 20 and 30 joined
    note: The prompt runs through the Mistral-Small-3.2-24B-Instruct-2506 language model. The hidden states of three of its layers are joined per token, giving one 15,360-wide vector per token for the transformer.
    side: true
  - id: noise
    role: input
    title: Random noise
    details:
    - in the latent space
    note: Generation starts from random numbers in the latent space of the FLUX.2 autoencoder, not from pixels.
  - id: pack
    role: step
    title: Pack into tokens
    details:
    - 2 × 2 latent patches per token
    note: Each 2 by 2 patch of the latents becomes one image token, and every token is given its position for the rotary position embedding.
  - id: transformer
    role: denoiser
    title: Flux 2 transformer
    details:
    - 8 double-stream blocks
    - 48 single-stream blocks · width 6144
    note: Double-stream blocks keep separate weights for image and text tokens and mix them in one attention; the sequences are then joined for the single-stream blocks. The step and the guidance value are embedded and modulate every block.
    frame: Denoising loop
  - id: scheduler
    role: step
    title: Scheduler step
    details:
    - flow matching · Euler
    note: Moves the latents one step towards a clean image using the transformer's prediction. The loop runs once per inference step; the Diffusers pipeline's default is 50 steps.
    frame: Denoising loop
  - id: vae
    role: autoencoder
    title: VAE decoder
    details:
    - latents to pixels
    note: The tokens are unpacked back into a latent image, and the decoder half of the FLUX.2 autoencoder turns it into pixels.
  - id: image
    role: outputHead
    title: Image
filters:
  size_category: large
  is_gated: true
  pipeline_tag: image-to-image
  library: diffusers
hub_signals:
  downloads_30d: 287646
  likes: 2606
  last_modified: '2026-11-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/black-forest-labs/FLUX.2-dev
aliases:
- black-forest-labs/FLUX.2-dev
lineage:
  parents: []
  children: []
quotes:
  summary: a 32 billion parameter rectified flow transformer capable of generating, editing and combining images based on text instructions
  intended_use: Open weights to drive new scientific research, and empower artists to develop innovative workflows.
  what_sets_it_apart: Trained using guidance distillation, making FLUX.2 [dev] more efficient.
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/black-forest-labs/FLUX.2-dev
  title: black-forest-labs/FLUX.2-dev — model page (Hugging Face)
  trust: 0.9
- url: https://github.com/black-forest-labs/flux2/blob/main/src/flux2/model.py
  title: 'black-forest-labs/flux2: src/flux2/model.py, model definition and FLUX.2 [dev] parameters (GitHub)'
  trust: 0.9
- url: https://github.com/black-forest-labs/flux2
  title: black-forest-labs/flux2 — reference implementation README (GitHub)
  trust: 0.9
- url: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/flux2/pipeline_flux2.py
  title: 'diffusers/pipelines/flux2/pipeline_flux2.py: reference pipeline (Hugging Face Diffusers)'
  trust: 0.9
---


