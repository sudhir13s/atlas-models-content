---
id: model/starcoder2-7b
type: model
slug: starcoder2-7b
schema_version: 1.1
title: starcoder2-7b
organization: organization/bigcode
release_date: '2024-02-20'
release_year: 2024
release_month: 2
license: license/bigcode-openrail-m
model_category: language
tasks:
- task/text-generation
parameters_active_b: 7.17
total_parameters: 7173923840
context_window_tokens: 16384
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 36
  kv_heads: 4
  head_size: 128
  hidden_size: 4608
  intermediate_size: 18432
  context_window_tokens: 16384
  vocabulary_size: 49152
  activation_function: gelu_pytorch_tanh
  normalization: LayerNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 20022
  likes: 227
  last_modified: '2024-06-11'
  observed_at: '2026-11-19'
  source: https://huggingface.co/bigcode/starcoder2-7b
aliases:
- bigcode/starcoder2-7b
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- starcoder2
- dense
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/bigcode/starcoder2-7b
  title: bigcode/starcoder2-7b — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/bigcode/starcoder2-7b/blob/main/config.json
  title: bigcode/starcoder2-7b — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2305.13245
  title: arXiv:2305.13245
  trust: 0.95
- url: https://arxiv.org/abs/2205.14135
  title: arXiv:2205.14135
  trust: 0.95
- url: https://arxiv.org/abs/2004.05150
  title: arXiv:2004.05150
  trust: 0.95
- url: https://arxiv.org/abs/2207.14255
  title: arXiv:2207.14255
  trust: 0.95
- url: https://arxiv.org/abs/2402.19173
  title: arXiv:2402.19173
  trust: 0.95
---


