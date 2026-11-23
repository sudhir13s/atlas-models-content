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
---

## Summary

LTX-Video is a video generation model from Lightricks. The model card lists it as a diffusion-based image-to-video model, meaning it animates a starting image into a short video, and describes it as DiT-based, using a diffusion transformer. The card says it produces 30 FPS (frames per second) video at 1216 by 704 resolution faster than the video can be watched.

## What Sets It Apart

The card describes real-time generation of high-resolution video and says the model was trained on a large dataset of diverse videos. The card covers a family of releases rather than one checkpoint: 13B and 2B sizes, distilled versions that are faster and use less video memory, and FP8 quantized versions.

## Running It

The card says the model works on resolutions divisible by 32 and frame counts of the form 8k + 1, such as 257, and pads the input otherwise. It advises resolutions below 720 by 1280 and under 257 frames, and that prompts should be in English and as detailed as possible.
