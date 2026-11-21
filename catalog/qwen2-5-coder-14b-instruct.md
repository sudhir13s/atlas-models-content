---
id: model/qwen2-5-coder-14b-instruct
type: model
slug: qwen2-5-coder-14b-instruct
schema_version: 1.1
title: Qwen2.5-Coder-14B-Instruct
organization: organization/qwen
release_date: '2024-11-06'
release_year: 2024
release_month: 11
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
- task/code-models
parameters_active_b: 14.77
total_parameters: 14770033664
context_window_tokens: 32768
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 40
  kv_heads: 8
  head_size: 128
  hidden_size: 5120
  intermediate_size: 13824
  context_window_tokens: 32768
  vocabulary_size: 152064
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
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 1579537
  likes: 220
  last_modified: '2025-01-12'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2.5-Coder-14B-Instruct
aliases:
- Qwen/Qwen2.5-Coder-14B-Instruct
- qwen2.5-coder-14b-instruct
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- qwen2
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-Coder-14B-Instruct
  title: Qwen/Qwen2.5-Coder-14B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-Coder-14B-Instruct/blob/main/config.json
  title: Qwen/Qwen2.5-Coder-14B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2409.12186
  title: arXiv:2409.12186
  trust: 0.95
- url: https://arxiv.org/abs/2309.00071
  title: arXiv:2309.00071
  trust: 0.95
- url: https://arxiv.org/abs/2407.10671
  title: arXiv:2407.10671
  trust: 0.95
---


