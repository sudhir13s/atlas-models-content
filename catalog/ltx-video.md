---
id: model/ltx-video
type: model
slug: ltx-video
schema_version: 1.1
title: LTX-Video
organization: organization/lightricks
release_date: '2024-10-31'
release_year: 2024
release_month: 10
license_note: other
model_category: generation
tasks:
- task/video-generation
total_parameters: 1923385472
summary: LTX-Video is a video generation model from Lightricks. The model card lists it as a diffusion-based image-to-video model, meaning it animates a starting image into a short video, and describes it as DiT-based, using a diffusion transformer. The card says it produces 30 FPS (frames per second) video at 1216 by 704 resolution faster than the video can be watched.
what_sets_it_apart: 'The card describes real-time generation of high-resolution video and says the model was trained on a large dataset of diverse videos. The card covers a family of releases rather than one checkpoint: 13B and 2B sizes, distilled versions that are faster and use less video memory, and FP8 quantized versions.'
running_it: The card says the model works on resolutions divisible by 32 and frame counts of the form 8k + 1, such as 257, and pads the input otherwise. It advises resolutions below 720 by 1280 and under 257 frames, and that prompts should be in English and as detailed as possible.
pipeline:
  source: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/ltx/pipeline_ltx.py
  summary: The video is made in the latent space of a video autoencoder that packs 32 by 32 pixels and 8 frames into one latent position. A T5 text encoder reads the prompt, a transformer cleans random noise step by step while reading it, and the decoder turns the result into frames. The figure follows the Diffusers-format weights in the repository's transformer and vae folders.
  side_into: cross-attention
  stages:
  - id: prompt
    role: input
    title: Text prompt
    side: true
  - id: tokenizer
    role: tokenizer
    title: T5 tokenizer
    details:
    - up to 128 tokens by default
    side: true
  - id: text-encoder
    role: textEncoder
    title: T5 text encoder
    details:
    - one 4096-wide vector per token
    note: Turns the prompt into a sequence of vectors. The transformer reads them through cross-attention at every step of the loop.
    side: true
  - id: noise
    role: input
    title: Random noise
    details:
    - in the latent space · 128 channels
    note: Generation starts from random numbers in the latent space of the video autoencoder. Each latent position stands for 32 by 32 pixels and 8 frames.
  - id: pack
    role: step
    title: Pack into tokens
    details:
    - one token per latent position
    note: The latent video is flattened into one sequence of tokens, each tagged with its frame, row and column for the rotary position embedding.
  - id: self-attention
    role: attention
    title: Self-attention
    details:
    - 32 heads · width 2048
    - over all video tokens
    note: Each of the 28 blocks of the video transformer starts with self-attention over all video tokens; the step conditions every block.
    frame: Denoising loop
    inner_frame: Video block
    inner_repeat: 28
  - id: cross-attention
    role: attention
    title: Cross-attention
    details:
    - reads the prompt vectors
    frame: Denoising loop
    inner_frame: Video block
  - id: feed-forward
    role: feedForward
    title: Feed-forward
    note: Each block ends with a feed-forward layer. The output of the last block is the prediction the scheduler needs to clean the latents.
    frame: Denoising loop
    inner_frame: Video block
  - id: scheduler
    role: step
    title: Scheduler step
    details:
    - flow matching · Euler
    note: Moves the latents one step towards a clean video using the transformer's prediction. The loop runs once per inference step; the pipeline's default is 50 steps.
    frame: Denoising loop
  - id: vae
    role: autoencoder
    title: Video VAE decoder
    details:
    - latents to frames
    note: The decoder half of the video autoencoder turns the cleaned latents into pixel frames.
  - id: video
    role: outputHead
    title: Video frames
filters:
  size_category: small
  is_gated: false
  pipeline_tag: image-to-video
  library: diffusers
hub_signals:
  downloads_30d: 1345403
  likes: 2430
  last_modified: '2025-07-16'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Lightricks/LTX-Video
aliases:
- Lightricks/LTX-Video
lineage:
  parents: []
  children: []
quotes:
  summary: It produces 30 FPS videos at a 1216×704 resolution faster than they can be watched.
  what_sets_it_apart: Trained on a large-scale dataset of diverse videos, the model generates high-resolution videos with realistic and varied content.
  running_it: The model works on resolutions that are divisible by 32 and number of frames that are divisible by 8 + 1 (e.g. 257).
tags:
- image-to-video
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Lightricks/LTX-Video
  title: Lightricks/LTX-Video — model page (Hugging Face)
  trust: 0.9
- url: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/ltx/pipeline_ltx.py
  title: 'diffusers/pipelines/ltx/pipeline_ltx.py: reference pipeline (Hugging Face Diffusers)'
  trust: 0.9
- url: https://huggingface.co/Lightricks/LTX-Video/blob/main/model_index.json
  title: 'Lightricks/LTX-Video — model_index.json'
  trust: 0.9
- url: https://huggingface.co/Lightricks/LTX-Video/blob/main/transformer/config.json
  title: 'Lightricks/LTX-Video — transformer/config.json'
  trust: 0.9
- url: https://huggingface.co/Lightricks/LTX-Video/blob/main/vae/config.json
  title: 'Lightricks/LTX-Video — vae/config.json'
  trust: 0.9
---

## Summary

LTX-Video is a video generation model from Lightricks. The model card lists it as a diffusion-based image-to-video model, meaning it animates a starting image into a short video, and describes it as DiT-based, using a diffusion transformer. The card says it produces 30 FPS (frames per second) video at 1216 by 704 resolution faster than the video can be watched.

## What Sets It Apart

The card describes real-time generation of high-resolution video and says the model was trained on a large dataset of diverse videos. The card covers a family of releases rather than one checkpoint: 13B and 2B sizes, distilled versions that are faster and use less video memory, and FP8 quantized versions.

## Running It

The card says the model works on resolutions divisible by 32 and frame counts of the form 8k + 1, such as 257, and pads the input otherwise. It advises resolutions below 720 by 1280 and under 257 frames, and that prompts should be in English and as detailed as possible.
