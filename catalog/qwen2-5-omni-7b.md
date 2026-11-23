---
id: model/qwen2-5-omni-7b
type: model
slug: qwen2-5-omni-7b
schema_version: 1.1
title: Qwen2.5-Omni-7B
organization: organization/qwen
release_date: '2025-03-22'
release_year: 2025
release_month: 3
license: license/apache-2-0
model_category: multimodal
tasks:
- task/any-to-any-models
parameters_active_b: 10.73
total_parameters: 10732225440
summary: Qwen2.5-Omni-7B is a multimodal model from Qwen that perceives text, images, audio and video as input and answers with text or with spoken audio. The card describes it as end-to-end, meaning one model handles all of these, and as able to generate text and natural speech responses in a streaming manner, that is, as the input arrives.
intended_use: The card presents it for real-time voice and video chat, with an architecture that takes chunked input and returns output immediately. It also describes following spoken instructions end to end, and reports results on speech recognition, translation, audio understanding, image reasoning and video understanding.
what_sets_it_apart: The card describes a Thinker-Talker architecture proposed by the Qwen team. It also describes TMRoPE (Time-aligned Multimodal RoPE), a position embedding that synchronizes the timestamps of video input with audio.
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: any-to-any
  library: transformers
hub_signals:
  downloads_30d: 253435
  likes: 1980
  last_modified: '2025-04-30'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Qwen/Qwen2.5-Omni-7B
aliases:
- Qwen/Qwen2.5-Omni-7B
lineage:
  parents: []
  children: []
quotes:
  summary: Qwen2.5-Omni is an end-to-end multimodal model designed to perceive diverse modalities, including text, images, audio, and video, while simultaneously generating text and natural speech responses in a streaming manner.
  intended_use: Architecture designed for fully real-time interactions, supporting chunked input and immediate output.
  what_sets_it_apart: We propose a novel position embedding, named TMRoPE (Time-aligned Multimodal RoPE), to synchronize the timestamps of video inputs with audio.
tags:
- any-to-any
- qwen2-5-omni
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-Omni-7B
  title: Qwen/Qwen2.5-Omni-7B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-Omni-7B/blob/main/config.json
  title: Qwen/Qwen2.5-Omni-7B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2503.20215
  title: arXiv:2503.20215
  trust: 0.95
---

## Summary

Qwen2.5-Omni-7B is a multimodal model from Qwen that perceives text, images, audio and video as input and answers with text or with spoken audio. The card describes it as end-to-end, meaning one model handles all of these, and as able to generate text and natural speech responses in a streaming manner, that is, as the input arrives.

## Intended Use

The card presents it for real-time voice and video chat, with an architecture that takes chunked input and returns output immediately. It also describes following spoken instructions end to end, and reports results on speech recognition, translation, audio understanding, image reasoning and video understanding.

## What Sets It Apart

The card describes a Thinker-Talker architecture proposed by the Qwen team. It also describes TMRoPE (Time-aligned Multimodal RoPE), a position embedding that synchronizes the timestamps of video input with audio.
