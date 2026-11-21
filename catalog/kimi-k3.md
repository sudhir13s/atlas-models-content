---
id: model/kimi-k3
type: model
slug: kimi-k3
schema_version: 1.1
title: Kimi-K3
organization: organization/moonshot-ai
release_date: '2026-06-13'
release_year: 2026
release_month: 6
license_note: kimi-k3
model_category: language
tasks:
- task/chat-models
- task/vision-language-models
total_parameters: 2779931837184
context_window_tokens: 1048576
fingerprint:
  attention_type: MLA
  num_layers: 93
  attention_heads: 96
  kv_heads: 96
  hidden_size: 7168
  intermediate_size: 33792
  context_window_tokens: 1048576
  vocabulary_size: 163840
  activation_function: situ
  normalization: RMSNorm
moe_config:
  expert_count: 896
  shared_expert: true
filters:
  size_category: xlarge
  is_quantized: false
  is_moe: true
  attention_type: MLA
  is_gated: false
  pipeline_tag: image-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 734802
  likes: 12006
  last_modified: '2026-11-20'
  observed_at: '2026-11-21'
  source: https://huggingface.co/moonshotai/Kimi-K3
aliases:
- moonshotai/Kimi-K3
lineage:
  parents: []
  children: []
tags:
- llm
- image-text-to-text
- kimi-k3
- moe
- mla
updated: '2026-11-21'
sources:
- url: https://huggingface.co/moonshotai/Kimi-K3
  title: moonshotai/Kimi-K3 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/moonshotai/Kimi-K3/blob/main/config.json
  title: moonshotai/Kimi-K3 — config.json
  trust: 0.9
---


