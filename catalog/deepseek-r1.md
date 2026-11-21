---
id: model/deepseek-r1
type: model
slug: deepseek-r1
schema_version: 1.1
title: DeepSeek-R1
organization: organization/deepseek
release_date: '2025-01-20'
release_year: 2025
release_month: 1
license: license/mit
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
- task/reasoning-models
total_parameters: 684489845504
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
  downloads_30d: 1634572
  likes: 14470
  last_modified: '2025-03-27'
  observed_at: '2026-11-20'
  source: https://huggingface.co/deepseek-ai/DeepSeek-R1
aliases:
- deepseek-ai/DeepSeek-R1
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- deepseek-v3
- moe
- mla
updated: '2026-11-21'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-R1
  title: deepseek-ai/DeepSeek-R1 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-R1/blob/main/config.json
  title: deepseek-ai/DeepSeek-R1 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2501.12948
  title: arXiv:2501.12948
  trust: 0.95
---


