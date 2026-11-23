---
id: model/qwen-image-edit
type: model
slug: qwen-image-edit
schema_version: 3.0
title: Qwen-Image-Edit
organization: organization/qwen
release_date: '2025-08-17'
license: license/apache-2-0
model_category: generation
model_kind: diffusion
tasks:
- task/image-editing
total_parameters: 20430401088
summary: Qwen-Image-Edit is the image-editing version of Qwen-Image, from Qwen. It takes an input image and a written instruction and produces an edited image. The card says it is built on the 20B Qwen-Image model and lists English and Chinese as its languages.
intended_use: The card names two kinds of editing. Appearance editing adds, removes or changes elements while the rest of the image stays unchanged. Semantic editing, such as IP creation, object rotation and style transfer, may change many pixels but keeps the meaning consistent. It also lists bilingual text editing, which adds, deletes or changes text in images while keeping the original font, size and style.
what_sets_it_apart: 'Qwen says it extends the text rendering ability of Qwen-Image to editing. The input image is fed into two paths at once: Qwen2.5-VL for visual semantic control, and the VAE (variational autoencoder) encoder for visual appearance control.'
running_it: The card says to install the latest version of the Diffusers library from GitHub. Its example loads the model with QwenImageEditPipeline in bfloat16 on a CUDA GPU, with 50 inference steps and a true_cfg_scale of 4.0.
pipeline:
  source: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/qwenimage/pipeline_qwenimage_edit.py
  summary: 'The input image is used twice: the Qwen2.5-VL vision-language model reads it together with the instruction (left), and the autoencoder turns it into latent tokens placed after the tokens of the image being made. A transformer cleans random noise step by step while reading both, and a decoder turns the result into the edited picture.'
  side_into: transformer
  stages:
  - id: instruction
    role: input
    title: Instruction + input image
    details:
    - inside a chat template
    side: true
  - id: text-encoder
    role: textEncoder
    title: Qwen2.5-VL encoder
    details:
    - last layer · width 3584
    note: The vision-language model reads the image and the instruction together. The hidden states of its last layer, without the fixed template tokens, are the conditioning vectors the transformer reads as text tokens.
    side: true
  - id: image
    role: input
    title: Input image
    details:
    - resized to about 1024 × 1024 area
  - id: vae-encoder
    role: autoencoder
    title: VAE encoder
    details:
    - image to latents · 16 channels
    note: The encoder half of the Qwen-Image autoencoder turns the input image into latents.
  - id: pack
    role: step
    title: Pack into tokens
    details:
    - 2 × 2 latent patches per token
    note: Each 2 by 2 patch of the latents becomes one image token.
  - id: join
    role: projector
    title: Join with noisy tokens
    details:
    - appended in the sequence
    note: The edited image starts as random noise in the latent space. At every step the input image tokens are appended after its tokens.
    frame: Denoising loop
  - id: transformer
    role: denoiser
    title: Qwen-Image transformer
    details:
    - 60 double-stream blocks
    - 24 heads · width 3072
    note: Every block keeps separate weights for image and text tokens and mixes them in one attention over both. Only the outputs at the positions of the image being made are kept.
    frame: Denoising loop
  - id: scheduler
    role: step
    title: Scheduler step
    details:
    - flow matching · Euler
    note: Moves the latents one step towards a clean image using the transformer's prediction. The loop runs once per inference step; the pipeline's default is 50 steps.
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
  size_category: large
  is_gated: false
  pipeline_tag: image-to-image
  library: diffusers
hub_signals:
  downloads_30d: 153157
  likes: 2587
  last_modified: '2025-08-25'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Qwen/Qwen-Image-Edit
aliases:
- Qwen/Qwen-Image-Edit
lineage:
  parents: []
  children: []
quotes:
  summary: We are excited to introduce Qwen-Image-Edit, the image editing version of Qwen-Image.
  intended_use: Qwen-Image-Edit supports bilingual (Chinese and English) text editing, allowing direct addition, deletion, and modification of text in images while preserving the original font, size, and style.
  what_sets_it_apart: Qwen-Image-Edit simultaneously feeds the input image into Qwen2.5-VL (for visual semantic control) and the VAE Encoder (for visual appearance control)
  running_it: Install the latest version of diffusers
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen-Image-Edit
  title: Qwen/Qwen-Image-Edit — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2508.02324
  title: arXiv:2508.02324
  trust: 0.95
- url: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/qwenimage/pipeline_qwenimage_edit.py
  title: 'diffusers/pipelines/qwenimage/pipeline_qwenimage_edit.py: reference pipeline (Hugging Face Diffusers)'
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen-Image-Edit/blob/main/model_index.json
  title: Qwen/Qwen-Image-Edit — model_index.json
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen-Image-Edit/blob/main/transformer/config.json
  title: Qwen/Qwen-Image-Edit — transformer/config.json
  trust: 0.9
---


