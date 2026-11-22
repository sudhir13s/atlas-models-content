---
id: model/gpt-oss-120b
type: model
slug: gpt-oss-120b
schema_version: 1.1
title: gpt-oss-120b
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
total_parameters: 116829156672
context_window_tokens: 131072
fingerprint:
  attention_type: GQA
  num_layers: 36
  attention_heads: 64
  kv_heads: 8
  head_size: 64
  hidden_size: 2880
  intermediate_size: 2880
  context_window_tokens: 131072
  vocabulary_size: 201088
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 150000.0
moe_config:
  expert_count: 128
  active_experts: 4
filters:
  size_category: xlarge
  is_quantized: true
  quant_format: mxfp4
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 2395705
  likes: 5546
  last_modified: '2025-08-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/openai/gpt-oss-120b
aliases:
- openai/gpt-oss-120b
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- gpt-oss
- moe
- gqa
updated: '2026-11-22'
sources:
- url: https://huggingface.co/openai/gpt-oss-120b
  title: openai/gpt-oss-120b — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai/gpt-oss-120b/blob/main/config.json
  title: openai/gpt-oss-120b — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2508.10925
  title: arXiv:2508.10925
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gpt_oss/modeling_gpt_oss.py
  title: 'transformers/models/gpt_oss/modeling_gpt_oss.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


