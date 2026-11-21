---
id: model/gpt-oss-20b
type: model
slug: gpt-oss-20b
schema_version: 1.1
title: gpt-oss-20b
organization: organization/openai
release_date: '2025-08-04'
release_year: 2025
release_month: 8
license: license/apache-2-0
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
- task/reasoning-models
- task/tool-calling-models
total_parameters: 20914757184
context_window_tokens: 131072
fingerprint:
  attention_type: GQA
  num_layers: 24
  attention_heads: 64
  kv_heads: 8
  head_size: 64
  hidden_size: 2880
  intermediate_size: 2880
  context_window_tokens: 131072
  vocabulary_size: 201088
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 150000.0
moe_config:
  expert_count: 32
  active_experts: 4
filters:
  size_category: large
  is_quantized: true
  quant_format: mxfp4
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 5560822
  likes: 5330
  last_modified: '2025-08-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/openai/gpt-oss-20b
aliases:
- openai/gpt-oss-20b
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- gpt-oss
- moe
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/openai/gpt-oss-20b
  title: openai/gpt-oss-20b — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai/gpt-oss-20b/blob/main/config.json
  title: openai/gpt-oss-20b — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2508.10925
  title: arXiv:2508.10925
  trust: 0.95
---


