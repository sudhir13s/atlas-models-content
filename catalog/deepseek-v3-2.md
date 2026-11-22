---
id: model/deepseek-v3-2
type: model
slug: deepseek-v3-2
schema_version: 1.1
title: DeepSeek-V3.2
organization: organization/deepseek
release_date: '2025-12-01'
release_year: 2025
release_month: 12
license: license/mit
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
total_parameters: 685355329792
context_window_tokens: 163840
fingerprint:
  attention_type: MLA
  num_layers: 61
  attention_heads: 128
  kv_heads: 128
  head_size: 56
  hidden_size: 7168
  intermediate_size: 18432
  context_window_tokens: 163840
  vocabulary_size: 129280
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 10000.0
moe_config:
  expert_count: 256
  active_experts: 8
  shared_expert: true
filters:
  size_category: xlarge
  is_quantized: true
  quant_format: fp8
  is_moe: true
  attention_type: MLA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 10436104
  likes: 1608
  last_modified: '2025-12-01'
  observed_at: '2026-11-20'
  source: https://huggingface.co/deepseek-ai/DeepSeek-V3.2
aliases:
- deepseek-ai/DeepSeek-V3.2
- deepseek-v3.2
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- deepseek-v32
- moe
- mla
updated: '2026-11-22'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-V3.2
  title: deepseek-ai/DeepSeek-V3.2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-V3.2/blob/main/config.json
  title: deepseek-ai/DeepSeek-V3.2 — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/deepseek_v32/modeling_deepseek_v32.py
  title: 'transformers/models/deepseek_v32/modeling_deepseek_v32.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


