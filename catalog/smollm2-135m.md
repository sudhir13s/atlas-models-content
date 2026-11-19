---
id: model/smollm2-135m
type: model
slug: smollm2-135m
schema_version: 1.1
title: SmolLM2-135M
organization: organization/hugging-face
release_date: '2024-10-31'
release_year: 2024
release_month: 10
license: license/apache-2-0
model_category: language
tasks:
- task/text-generation
parameters_active_b: 0.13
total_parameters: 134515008
context_window_tokens: 8192
fingerprint:
  attention_type: GQA
  num_layers: 30
  attention_heads: 9
  kv_heads: 3
  head_size: 64
  hidden_size: 576
  intermediate_size: 1536
  context_window_tokens: 8192
  vocabulary_size: 49152
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 100000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 2303544
  likes: 270
  last_modified: '2025-02-06'
  observed_at: '2026-11-19'
  source: https://huggingface.co/HuggingFaceTB/SmolLM2-135M
aliases:
- HuggingFaceTB/SmolLM2-135M
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- llama
- dense
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/HuggingFaceTB/SmolLM2-135M
  title: HuggingFaceTB/SmolLM2-135M — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/HuggingFaceTB/SmolLM2-135M/blob/main/config.json
  title: HuggingFaceTB/SmolLM2-135M — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2502.02737
  title: arXiv:2502.02737
  trust: 0.95
---


