---
id: model/kimi-k2-5
type: model
slug: kimi-k2-5
schema_version: 1.1
title: Kimi-K2.5
organization: organization/moonshot-ai
release_date: '2026-01-01'
release_year: 2026
release_month: 1
license_note: modified-mit
model_category: multimodal
tasks:
- task/vision-language-models
total_parameters: 1026879376368
context_window_tokens: 262144
fingerprint:
  attention_type: MLA
  num_layers: 61
  attention_heads: 64
  kv_heads: 64
  head_size: 112
  hidden_size: 7168
  intermediate_size: 18432
  context_window_tokens: 262144
  vocabulary_size: 163840
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 50000.0
moe_config:
  expert_count: 384
  active_experts: 8
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
  downloads_30d: 394729
  likes: 2902
  last_modified: '2026-04-30'
  observed_at: '2026-11-19'
  source: https://huggingface.co/moonshotai/Kimi-K2.5
variants:
- name: nvidia/Kimi-K2.5-NVFP4
  format: nvfp4
  address: https://huggingface.co/nvidia/Kimi-K2.5-NVFP4
  publisher: nvidia
aliases:
- moonshotai/Kimi-K2.5
- kimi-k2.5-nvfp4
lineage:
  parents: []
  children: []
tags:
- llm
- image-text-to-text
- kimi-k25
- moe
- mla
updated: '2026-11-19'
sources:
- url: https://huggingface.co/moonshotai/Kimi-K2.5
  title: moonshotai/Kimi-K2.5 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/moonshotai/Kimi-K2.5/blob/main/config.json
  title: moonshotai/Kimi-K2.5 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2602.02276
  title: arXiv:2602.02276
  trust: 0.95
---


