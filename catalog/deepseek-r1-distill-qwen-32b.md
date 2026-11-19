---
id: model/deepseek-r1-distill-qwen-32b
type: model
slug: deepseek-r1-distill-qwen-32b
schema_version: 1.1
title: DeepSeek-R1-Distill-Qwen-32B
organization: organization/deepseek
release_date: '2025-01-20'
release_year: 2025
release_month: 1
license: license/mit
model_category: language
parameters_active_b: 32.76
total_parameters: 32763876352
context_window_tokens: 131072
fingerprint:
  attention_type: GQA
  num_layers: 64
  attention_heads: 40
  kv_heads: 8
  head_size: 128
  hidden_size: 5120
  intermediate_size: 27648
  context_window_tokens: 131072
  vocabulary_size: 152064
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: large
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 368137
  likes: 1677
  last_modified: '2025-02-24'
  observed_at: '2026-11-19'
  source: https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-32B
aliases:
- deepseek-ai/DeepSeek-R1-Distill-Qwen-32B
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- qwen2
- dense
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-32B
  title: deepseek-ai/DeepSeek-R1-Distill-Qwen-32B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-32B/blob/main/config.json
  title: deepseek-ai/DeepSeek-R1-Distill-Qwen-32B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2501.12948
  title: arXiv:2501.12948
  trust: 0.95
---


