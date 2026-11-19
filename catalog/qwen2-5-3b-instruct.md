---
id: model/qwen2-5-3b-instruct
type: model
slug: qwen2-5-3b-instruct
schema_version: 1.1
title: Qwen2.5-3B-Instruct
organization: organization/qwen
release_date: '2024-09-17'
release_year: 2024
release_month: 9
license_note: qwen-research
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 3.09
total_parameters: 3085938688
context_window_tokens: 32768
fingerprint:
  attention_type: GQA
  num_layers: 36
  attention_heads: 16
  kv_heads: 2
  head_size: 128
  hidden_size: 2048
  intermediate_size: 11008
  context_window_tokens: 32768
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 1863245
  likes: 642
  last_modified: '2024-09-25'
  observed_at: '2026-11-19'
  source: https://huggingface.co/Qwen/Qwen2.5-3B-Instruct
aliases:
- Qwen/Qwen2.5-3B-Instruct
- qwen2.5-3b-instruct
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- qwen2
- dense
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-3B-Instruct
  title: Qwen/Qwen2.5-3B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-3B-Instruct/blob/main/config.json
  title: Qwen/Qwen2.5-3B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2407.10671
  title: arXiv:2407.10671
  trust: 0.95
---


