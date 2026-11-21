---
id: model/smollm3-3b
type: model
slug: smollm3-3b
schema_version: 1.1
title: SmolLM3-3B
organization: organization/hugging-face
release_date: '2025-07-08'
release_year: 2025
release_month: 7
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
- task/tool-calling-models
parameters_active_b: 3.08
total_parameters: 3075098624
context_window_tokens: 65536
fingerprint:
  attention_type: GQA
  num_layers: 36
  attention_heads: 16
  kv_heads: 4
  head_size: 128
  hidden_size: 2048
  intermediate_size: 11008
  context_window_tokens: 65536
  vocabulary_size: 128256
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 5000000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 161445
  likes: 1083
  last_modified: '2025-09-10'
  observed_at: '2026-11-20'
  source: https://huggingface.co/HuggingFaceTB/SmolLM3-3B
aliases:
- HuggingFaceTB/SmolLM3-3B
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- smollm3
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/HuggingFaceTB/SmolLM3-3B
  title: HuggingFaceTB/SmolLM3-3B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/HuggingFaceTB/SmolLM3-3B/blob/main/config.json
  title: HuggingFaceTB/SmolLM3-3B — config.json
  trust: 0.9
---


