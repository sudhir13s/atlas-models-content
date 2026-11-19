---
id: model/smollm2-1-7b-instruct
type: model
slug: smollm2-1-7b-instruct
schema_version: 1.1
title: SmolLM2-1.7B-Instruct
organization: organization/hugging-face
release_date: '2024-10-31'
release_year: 2024
release_month: 10
license: license/apache-2-0
model_category: language
parameters_active_b: 1.71
total_parameters: 1711376384
context_window_tokens: 8192
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 32
  kv_heads: 32
  head_size: 64
  hidden_size: 2048
  intermediate_size: 8192
  context_window_tokens: 8192
  vocabulary_size: 49152
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 130000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 251144
  likes: 772
  last_modified: '2025-04-21'
  observed_at: '2026-11-19'
  source: https://huggingface.co/HuggingFaceTB/SmolLM2-1.7B-Instruct
aliases:
- HuggingFaceTB/SmolLM2-1.7B-Instruct
- smollm2-1.7b-instruct
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- llama
- dense
- mha
updated: '2026-11-19'
sources:
- url: https://huggingface.co/HuggingFaceTB/SmolLM2-1.7B-Instruct
  title: HuggingFaceTB/SmolLM2-1.7B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/HuggingFaceTB/SmolLM2-1.7B-Instruct/blob/main/config.json
  title: HuggingFaceTB/SmolLM2-1.7B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2502.02737
  title: arXiv:2502.02737
  trust: 0.95
---


