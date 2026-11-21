---
id: model/audio-flamingo-3-hf
type: model
slug: audio-flamingo-3-hf
schema_version: 1.1
title: audio-flamingo-3-hf
organization: organization/nvidia
release_date: '2025-10-24'
release_year: 2025
release_month: 10
license_note: other
model_category: audio
tasks:
- task/speech-language-models
parameters_active_b: 8.27
total_parameters: 8267215360
context_window_tokens: 32768
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 28
  kv_heads: 4
  head_size: 128
  hidden_size: 3584
  intermediate_size: 18944
  context_window_tokens: 32768
  vocabulary_size: 151672
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: audio-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 81378
  likes: 200
  last_modified: '2026-04-13'
  observed_at: '2026-11-21'
  source: https://huggingface.co/nvidia/audio-flamingo-3-hf
aliases:
- nvidia/audio-flamingo-3-hf
lineage:
  parents: []
  children: []
tags:
- audio-text-to-text
- audioflamingo3
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/nvidia/audio-flamingo-3-hf
  title: nvidia/audio-flamingo-3-hf — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/audio-flamingo-3-hf/blob/main/config.json
  title: nvidia/audio-flamingo-3-hf — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2507.08128
  title: arXiv:2507.08128
  trust: 0.95
- url: https://arxiv.org/abs/2505.13032
  title: arXiv:2505.13032
  trust: 0.95
---


