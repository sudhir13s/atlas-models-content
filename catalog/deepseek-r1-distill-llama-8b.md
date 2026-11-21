---
id: model/deepseek-r1-distill-llama-8b
type: model
slug: deepseek-r1-distill-llama-8b
schema_version: 1.1
title: DeepSeek-R1-Distill-Llama-8B
organization: organization/deepseek
release_date: '2025-01-20'
release_year: 2025
release_month: 1
license: license/mit
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
- task/reasoning-models
parameters_active_b: 8.03
total_parameters: 8030261248
context_window_tokens: 131072
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 14336
  context_window_tokens: 131072
  vocabulary_size: 128256
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 500000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 76737
  likes: 921
  last_modified: '2025-02-24'
  observed_at: '2026-11-20'
  source: https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Llama-8B
aliases:
- deepseek-ai/DeepSeek-R1-Distill-Llama-8B
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- llama
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Llama-8B
  title: deepseek-ai/DeepSeek-R1-Distill-Llama-8B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Llama-8B/blob/main/config.json
  title: deepseek-ai/DeepSeek-R1-Distill-Llama-8B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2501.12948
  title: arXiv:2501.12948
  trust: 0.95
---


