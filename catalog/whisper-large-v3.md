---
id: model/whisper-large-v3
type: model
slug: whisper-large-v3
schema_version: 1.1
title: whisper-large-v3
organization: organization/openai
release_date: '2023-11-07'
release_year: 2023
release_month: 11
license: license/apache-2-0
model_category: audio
tasks:
- task/speech-recognition
parameters_active_b: 1.54
total_parameters: 1543490560
fingerprint:
  num_layers: 32
  hidden_size: 1280
  vocabulary_size: 51866
  activation_function: gelu
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: automatic-speech-recognition
  library: transformers
hub_signals:
  downloads_30d: 3303148
  likes: 6832
  last_modified: '2024-08-12'
  observed_at: '2026-11-21'
  source: https://huggingface.co/openai/whisper-large-v3
aliases:
- openai/whisper-large-v3
lineage:
  parents: []
  children: []
tags:
- automatic-speech-recognition
- whisper
- dense
updated: '2026-11-21'
sources:
- url: https://huggingface.co/openai/whisper-large-v3
  title: openai/whisper-large-v3 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai/whisper-large-v3/blob/main/config.json
  title: openai/whisper-large-v3 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2212.04356
  title: arXiv:2212.04356
  trust: 0.95
---


