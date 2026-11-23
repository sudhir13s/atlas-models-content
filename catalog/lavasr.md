---
id: model/lavasr
type: model
slug: lavasr
schema_version: 1.1
title: LavaSR
organization: organization/yatharths
release_date: '2026-02-11'
release_year: 2026
release_month: 2
license: license/apache-2-0
model_category: audio
tasks:
- task/audio-enhancement
summary: LavaSR v2 is a small audio enhancement model published by YatharthS on Hugging Face. It performs bandwidth extension, which rebuilds the high frequencies that low-quality or low-sample-rate audio has lost, and it is paired with a denoiser. The card says the model is 50 MB, accepts audio at any rate from 8 to 48 kHz, and outputs 48 kHz audio.
intended_use: 'The card lists three uses: restoring low-quality audio datasets, improving the quality of text-to-speech and speech-recognition models, and upscaling poor-quality voice calls.'
what_sets_it_apart: The card claims that the model can enhance nearly 5000 seconds of audio in one second, and that its quality exceeds that of diffusion models of 6 GB. It gives an inference speed of 20 to 80 times real time on a CPU and 800 to 5000 times real time depending on the GPU.
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: audio-to-audio
hub_signals:
  downloads_30d: 1276
  likes: 91
  last_modified: '2026-02-27'
  observed_at: '2026-11-21'
  source: https://huggingface.co/YatharthS/LavaSR
aliases:
- YatharthS/LavaSR
lineage:
  parents: []
  children: []
quotes:
  summary: LavaSR(v2) is a novel 50MB BWE(bandwidth extension) model along with the UL-UNAS denoiser.
  intended_use: Restore low quality audio datasets
  what_sets_it_apart: It can enhance nearly 5000 seconds of audio in just 1 second while exceeding the quality of 6gb large diffusion models.
tags:
- audio-to-audio
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/YatharthS/LavaSR
  title: YatharthS/LavaSR — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/YatharthS/LavaSR/blob/main/config.json
  title: YatharthS/LavaSR — config.json
  trust: 0.9
---

## Summary

LavaSR v2 is a small audio enhancement model published by YatharthS on Hugging Face. It performs bandwidth extension, which rebuilds the high frequencies that low-quality or low-sample-rate audio has lost, and it is paired with a denoiser. The card says the model is 50 MB, accepts audio at any rate from 8 to 48 kHz, and outputs 48 kHz audio.

## Intended Use

The card lists three uses: restoring low-quality audio datasets, improving the quality of text-to-speech and speech-recognition models, and upscaling poor-quality voice calls.

## What Sets It Apart

The card claims that the model can enhance nearly 5000 seconds of audio in one second, and that its quality exceeds that of diffusion models of 6 GB. It gives an inference speed of 20 to 80 times real time on a CPU and 800 to 5000 times real time depending on the GPU.
