---
id: model/autotrust-decider-pro-35b
type: model
slug: autotrust-decider-pro-35b
schema_version: 1.1
title: AutoTrust-Decider-Pro-35B
organization: organization/autotrust
release_date: '2026-10-11'
release_year: 2026
release_month: 10
license: license/apache-2-0
model_category: language
tasks:
- task/text-classification
total_parameters: 35951822704
context_window_tokens: 262144
fingerprint:
  attention_type: GQA
  num_layers: 40
  attention_heads: 16
  kv_heads: 2
  head_size: 256
  hidden_size: 2048
  context_window_tokens: 262144
  vocabulary_size: 248320
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000000.0
moe_config:
  expert_count: 256
  active_experts: 8
  shared_expert: true
filters:
  size_category: large
  is_quantized: false
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-classification
  library: transformers
hub_signals:
  downloads_30d: 927364
  likes: 4351
  last_modified: '2026-10-14'
  observed_at: '2026-11-21'
  source: https://huggingface.co/autotrust/AutoTrust-Decider-Pro-35B
aliases:
- autotrust/AutoTrust-Decider-Pro-35B
lineage:
  parents: []
  children: []
tags:
- llm
- text-classification
- qwen3-5-moe
- moe
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/autotrust/AutoTrust-Decider-Pro-35B
  title: autotrust/AutoTrust-Decider-Pro-35B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/autotrust/AutoTrust-Decider-Pro-35B/blob/main/config.json
  title: autotrust/AutoTrust-Decider-Pro-35B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2609.14973
  title: arXiv:2609.14973
  trust: 0.95
---


