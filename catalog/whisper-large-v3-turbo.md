---
id: model/whisper-large-v3-turbo
type: model
slug: whisper-large-v3-turbo
schema_version: 1.1
title: whisper-large-v3-turbo
organization: organization/openai
release_date: '2024-10-01'
release_year: 2024
release_month: 10
license: license/mit
model_category: audio
tasks:
- task/speech-recognition
parameters_active_b: 0.81
total_parameters: 808878080
fingerprint:
  num_layers: 32
  hidden_size: 1280
  vocabulary_size: 51866
  activation_function: gelu
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: automatic-speech-recognition
  library: transformers
hub_signals:
  downloads_30d: 4713890
  likes: 3562
  last_modified: '2024-10-04'
  observed_at: '2026-11-21'
  source: https://huggingface.co/openai/whisper-large-v3-turbo
aliases:
- openai/whisper-large-v3-turbo
lineage:
  parents: []
  children: []
tags:
- automatic-speech-recognition
- whisper
- dense
updated: '2026-11-21'
sources:
- url: https://huggingface.co/openai/whisper-large-v3-turbo
  title: openai/whisper-large-v3-turbo — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai/whisper-large-v3-turbo/blob/main/config.json
  title: openai/whisper-large-v3-turbo — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2212.04356
  title: arXiv:2212.04356
  trust: 0.95
---


