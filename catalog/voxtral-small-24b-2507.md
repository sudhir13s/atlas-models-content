---
id: model/voxtral-small-24b-2507
type: model
slug: voxtral-small-24b-2507
schema_version: 1.1
title: Voxtral-Small-24B-2507
organization: organization/mistral-ai
release_date: '2025-07-01'
release_year: 2025
release_month: 7
license: license/apache-2-0
model_category: audio
tasks:
- task/speech-language-models
parameters_active_b: 24.26
total_parameters: 24261800960
context_window_tokens: 131072
fingerprint:
  attention_type: GQA
  num_layers: 40
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 5120
  intermediate_size: 32768
  context_window_tokens: 131072
  vocabulary_size: 131072
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 100000000.0
filters:
  size_category: large
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: audio-text-to-text
  library: vllm
hub_signals:
  downloads_30d: 7652
  likes: 557
  last_modified: '2025-12-20'
  observed_at: '2026-11-21'
  source: https://huggingface.co/mistralai/Voxtral-Small-24B-2507
aliases:
- mistralai/Voxtral-Small-24B-2507
lineage:
  parents: []
  children: []
tags:
- audio-text-to-text
- voxtral
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/mistralai/Voxtral-Small-24B-2507
  title: mistralai/Voxtral-Small-24B-2507 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/mistralai/Voxtral-Small-24B-2507/blob/main/config.json
  title: mistralai/Voxtral-Small-24B-2507 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2507.13264
  title: arXiv:2507.13264
  trust: 0.95
---


