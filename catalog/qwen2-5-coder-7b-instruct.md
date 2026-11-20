---
id: model/qwen2-5-coder-7b-instruct
type: model
slug: qwen2-5-coder-7b-instruct
schema_version: 1.1
title: Qwen2.5-Coder-7B-Instruct
organization: organization/qwen
release_date: '2024-09-17'
release_year: 2024
release_month: 9
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 7.62
total_parameters: 7615616512
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
  downloads_30d: 1524077
  likes: 875
  last_modified: '2025-01-12'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2.5-Coder-7B-Instruct
variants:
- name: Qwen/Qwen2.5-Coder-7B-Instruct-GPTQ-Int4
  format: gptq
  address: https://huggingface.co/Qwen/Qwen2.5-Coder-7B-Instruct-GPTQ-Int4
  publisher: Qwen
aliases:
- Qwen/Qwen2.5-Coder-7B-Instruct
- qwen2.5-coder-7b-instruct
- qwen2.5-coder-7b-instruct-gptq-int4
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- qwen2
- dense
- gqa
updated: '2026-11-20'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-Coder-7B-Instruct
  title: Qwen/Qwen2.5-Coder-7B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-Coder-7B-Instruct/blob/main/config.json
  title: Qwen/Qwen2.5-Coder-7B-Instruct — config.json
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


