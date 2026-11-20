---
id: model/phi-3-5-mini-instruct
type: model
slug: phi-3-5-mini-instruct
schema_version: 1.1
title: Phi-3.5-mini-instruct
organization: organization/microsoft
release_date: '2024-08-16'
release_year: 2024
release_month: 8
license: license/mit
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 3.82
total_parameters: 3821079552
context_window_tokens: 131072
fingerprint:
  attention_type: MHA
  num_layers: 32
  attention_heads: 32
  kv_heads: 32
  head_size: 96
  hidden_size: 3072
  intermediate_size: 8192
  context_window_tokens: 131072
  vocabulary_size: 32064
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 297535
  likes: 1297
  last_modified: '2025-12-10'
  observed_at: '2026-11-20'
  source: https://huggingface.co/microsoft/Phi-3.5-mini-instruct
aliases:
- microsoft/Phi-3.5-mini-instruct
- phi-3.5-mini-instruct
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- phi3
- dense
- mha
updated: '2026-11-20'
sources:
- url: https://huggingface.co/microsoft/Phi-3.5-mini-instruct
  title: microsoft/Phi-3.5-mini-instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/Phi-3.5-mini-instruct/blob/main/config.json
  title: microsoft/Phi-3.5-mini-instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2404.14219
  title: arXiv:2404.14219
  trust: 0.95
- url: https://arxiv.org/abs/2407.13833
  title: arXiv:2407.13833
  trust: 0.95
- url: https://arxiv.org/abs/2403.06412
  title: arXiv:2403.06412
  trust: 0.95
---


