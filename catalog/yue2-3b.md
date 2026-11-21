---
id: model/yue2-3b
type: model
slug: yue2-3b
schema_version: 1.1
title: YuE2-3B
organization: organization/m-a-p
release_date: '2026-09-09'
release_year: 2026
release_month: 9
license_note: cc-by-nc-4.0
model_category: generation
tasks:
- task/music-and-sound-generation
parameters_active_b: 3.63
total_parameters: 3630684224
context_window_tokens: 24576
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 16
  kv_heads: 8
  head_size: 128
  hidden_size: 2048
  intermediate_size: 6144
  context_window_tokens: 24576
  vocabulary_size: 184704
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-to-audio
hub_signals:
  downloads_30d: 20330
  likes: 1267
  last_modified: '2026-10-06'
  observed_at: '2026-11-21'
  source: https://huggingface.co/m-a-p/YuE2-3B
aliases:
- m-a-p/YuE2-3B
lineage:
  parents: []
  children: []
tags:
- text-to-audio
- yue2
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/m-a-p/YuE2-3B
  title: m-a-p/YuE2-3B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/m-a-p/YuE2-3B/blob/main/config.json
  title: m-a-p/YuE2-3B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2610.05336
  title: arXiv:2610.05336
  trust: 0.95
- url: https://arxiv.org/abs/2609.33757
  title: arXiv:2609.33757
  trust: 0.95
- url: https://arxiv.org/abs/2503.08638
  title: arXiv:2503.08638
  trust: 0.95
---


