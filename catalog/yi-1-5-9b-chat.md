---
id: model/yi-1-5-9b-chat
type: model
slug: yi-1-5-9b-chat
schema_version: 1.1
title: Yi-1.5-9B-Chat
organization: organization/01-ai
release_date: '2024-05-10'
release_year: 2024
release_month: 5
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 8.83
total_parameters: 8829407232
context_window_tokens: 4096
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 32
  kv_heads: 4
  head_size: 128
  hidden_size: 4096
  intermediate_size: 11008
  context_window_tokens: 4096
  vocabulary_size: 64000
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 5000000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 18273
  likes: 149
  last_modified: '2024-06-26'
  observed_at: '2026-11-19'
  source: https://huggingface.co/01-ai/Yi-1.5-9B-Chat
aliases:
- 01-ai/Yi-1.5-9B-Chat
- yi-1.5-9b-chat
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- llama
- dense
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/01-ai/Yi-1.5-9B-Chat
  title: 01-ai/Yi-1.5-9B-Chat — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/01-ai/Yi-1.5-9B-Chat/blob/main/config.json
  title: 01-ai/Yi-1.5-9B-Chat — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2403.04652
  title: arXiv:2403.04652
  trust: 0.95
---


