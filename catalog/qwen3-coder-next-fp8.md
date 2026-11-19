---
id: model/qwen3-coder-next-fp8
type: model
slug: qwen3-coder-next-fp8
schema_version: 1.1
title: Qwen3-Coder-Next-FP8
organization: organization/qwen
release_date: '2026-02-01'
release_year: 2026
release_month: 2
license: license/apache-2-0
model_category: language
total_parameters: 79674391296
context_window_tokens: 262144
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 16
  kv_heads: 2
  head_size: 256
  hidden_size: 2048
  intermediate_size: 5120
  context_window_tokens: 262144
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 5000000.0
moe_config:
  expert_count: 512
  active_experts: 10
  shared_expert: true
filters:
  size_category: xlarge
  is_quantized: true
  quant_format: fp8
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 120411
  likes: 212
  last_modified: '2026-02-03'
  observed_at: '2026-11-19'
  source: https://huggingface.co/Qwen/Qwen3-Coder-Next-FP8
aliases:
- Qwen/Qwen3-Coder-Next-FP8
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- qwen3-next
- moe
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/Qwen/Qwen3-Coder-Next-FP8
  title: Qwen/Qwen3-Coder-Next-FP8 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-Coder-Next-FP8/blob/main/config.json
  title: Qwen/Qwen3-Coder-Next-FP8 — config.json
  trust: 0.9
---


