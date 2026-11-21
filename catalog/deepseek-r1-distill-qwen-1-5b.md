---
id: model/deepseek-r1-distill-qwen-1-5b
type: model
slug: deepseek-r1-distill-qwen-1-5b
schema_version: 1.1
title: DeepSeek-R1-Distill-Qwen-1.5B
organization: organization/deepseek
release_date: '2025-01-20'
release_year: 2025
release_month: 1
license: license/mit
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 1.78
total_parameters: 1777088000
context_window_tokens: 131072
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 12
  kv_heads: 2
  head_size: 128
  hidden_size: 1536
  intermediate_size: 8960
  context_window_tokens: 131072
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 424192
  likes: 1672
  last_modified: '2025-02-24'
  observed_at: '2026-11-20'
  source: https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B
aliases:
- deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B
- deepseek-r1-distill-qwen-1.5b
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- qwen2
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B
  title: deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B/blob/main/config.json
  title: deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2501.12948
  title: arXiv:2501.12948
  trust: 0.95
---


