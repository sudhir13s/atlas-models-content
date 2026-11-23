---
id: model/flux-1-dev
type: model
slug: flux-1-dev
schema_version: 3.0
title: FLUX.1-dev
organization: organization/black-forest-labs
release_date: '2024-07-31'
license_note: flux-1-dev-non-commercial-license
model_category: generation
model_kind: diffusion
tasks:
- task/image-generation
total_parameters: 11901408320
summary: 'FLUX.1 [dev] is a text-to-image model from Black Forest Labs: it generates an image from a written description. Its card calls it a 12 billion parameter rectified flow transformer, a type of image-generating neural network. The weights are open, released under the FLUX.1 [dev] Non-Commercial License, and the repository is gated, so a user must agree to the license before downloading.'
intended_use: The card says the open weights are meant to drive new scientific research and to let artists develop new workflows, and it points developers to a reference implementation as a starting point. It says generated outputs may be used for personal, scientific and commercial purposes as described in the license. Its out-of-scope list includes breaking the law, harming minors, spreading false information to harm others, exposing personal data, harassment, non-consensual nudity or illegal pornography, fully automated decisions about a person's legal rights, and large-scale disinformation.
what_sets_it_apart: The card says the model was trained using guidance distillation, which it says makes the model more efficient. Black Forest Labs also claims, in the card's own words, output quality second only to its FLUX.1 [pro] model and prompt following that matches closed source alternatives.
running_it: The card shows running it with the Diffusers library in bfloat16 precision, with a 1024 by 1024 image, a guidance scale of 3.5, 50 inference steps and a maximum prompt length of 512 tokens. It suggests CPU offloading to save graphics memory, and says the model is also available in ComfyUI for local use.
limitations: The card says the model is not intended or able to provide factual information, that as a statistical model it might amplify existing societal biases, and that it may fail to generate output that matches the prompts. It adds that prompt following is heavily influenced by the prompting style.
pipeline:
  source: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/flux/pipeline_flux.py
  summary: The image is made in the latent space of an autoencoder, not in pixels. A CLIP text encoder and a T5 text encoder read the prompt, a transformer cleans random noise step by step while reading their output, and a decoder turns the result into the picture.
  stages:
  - id: prompt
    role: input
    title: Text prompt
    side: true
    into: double-stream
  - id: t5
    role: textEncoder
    title: T5 tokenizer + encoder
    details:
    - one vector per token
    side: true
    note: T5 gives one vector per token, which the transformer reads as text tokens.
  - id: prompt-again
    role: input
    title: The same prompt
    side: true
    into: double-stream
  - id: clip
    role: textEncoder
    title: CLIP tokenizer + encoder
    details:
    - one pooled vector
    side: true
    note: CLIP gives one pooled vector for the whole prompt, which conditions every block together with the step.
  - id: noise
    role: input
    title: Random noise
    details:
    - in the latent space · 16 channels
    note: Generation starts from random numbers in the latent space of the autoencoder, not from pixels.
  - id: pack
    role: step
    title: Pack into tokens
    details:
    - 2 × 2 latent patches per token
    note: Each 2 by 2 patch of the latents becomes one image token, and every token is given its row and column for the rotary position embedding.
  - id: double-stream
    role: denoiser
    title: Double-stream blocks
    details:
    - image and text tokens, own weights
    note: Double-stream blocks keep separate weights for image and text tokens and mix them in one attention over both.
    frame: Denoising loop
  - id: single-stream
    role: denoiser
    title: Single-stream block
    details:
    - one joined sequence
    note: The two sequences are then joined and pass 38 single-stream blocks; the text tokens are dropped and the image tokens give the prediction the scheduler uses.
    frame: Denoising loop
    inner_frame: Single block
    inner_repeat: 38
  - id: scheduler
    role: step
    title: Scheduler step
    details:
    - flow matching · Euler
    note: Moves the latents one step towards a clean image using the transformer's prediction. The loop runs once per inference step; the card's example uses 50 steps.
    frame: Denoising loop
  - id: vae
    role: autoencoder
    title: VAE decoder
    details:
    - latents to pixels
    note: The tokens are unpacked back into a latent image, and the decoder half of the autoencoder turns it into pixels.
  - id: image
    role: outputHead
    title: Image
filters:
  size_category: medium
  is_gated: true
  pipeline_tag: text-to-image
  library: diffusers
hub_signals:
  downloads_30d: 427087
  likes: 16274
  last_modified: '2026-11-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/black-forest-labs/FLUX.1-dev
aliases:
- black-forest-labs/FLUX.1-dev
lineage:
  parents: []
  children: []
quotes:
  summary: FLUX.1 [dev] is a 12 billion parameter rectified flow transformer capable of generating images from text descriptions.
  intended_use: Open weights to drive new scientific research, and empower artists to develop innovative workflows.
  what_sets_it_apart: Trained using guidance distillation, making FLUX.1 [dev] more efficient.
  running_it: 'pipe.enable_model_cpu_offload() #save some VRAM by offloading the model to CPU.'
  limitations: This model is not intended or able to provide factual information.
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/black-forest-labs/FLUX.1-dev
  title: black-forest-labs/FLUX.1-dev — model page (Hugging Face)
  trust: 0.9
- url: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/flux/pipeline_flux.py
  title: 'diffusers/pipelines/flux/pipeline_flux.py: reference pipeline (Hugging Face Diffusers)'
  trust: 0.9
- url: https://arxiv.org/abs/2506.15742
  title: arXiv:2506.15742 — FLUX.1 Kontext (section 2 describes the FLUX.1 architecture)
  trust: 0.95
---


