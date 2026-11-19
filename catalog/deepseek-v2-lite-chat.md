---
id: model/deepseek-v2-lite-chat
type: model
slug: deepseek-v2-lite-chat
schema_version: 1.1
title: DeepSeek-V2-Lite-Chat
organization: organization/deepseek
release_date: '2024-05-15'
release_year: 2024
release_month: 5
license: license/deepseek
model_category: language
tasks:
- task/text-generation
total_parameters: 15706484224
context_window_tokens: 163840
fingerprint:
  attention_type: MLA
  num_layers: 27
  attention_heads: 16
  kv_heads: 16
  head_size: 128
  hidden_size: 2048
  intermediate_size: 10944
  context_window_tokens: 163840
  vocabulary_size: 102400
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
moe_config:
  expert_count: 64
  active_experts: 6
  shared_expert: true
filters:
  size_category: large
  is_quantized: false
  is_moe: true
  attention_type: MLA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 64363
  likes: 173
  last_modified: '2024-06-25'
  observed_at: '2026-11-19'
  source: https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat
aliases:
- deepseek-ai/DeepSeek-V2-Lite-Chat
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- deepseek-v2
- moe
- mla
updated: '2026-11-19'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat
  title: deepseek-ai/DeepSeek-V2-Lite-Chat — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat/blob/main/config.json
  title: deepseek-ai/DeepSeek-V2-Lite-Chat — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2405.04434
  title: arXiv:2405.04434
  trust: 0.95
---


