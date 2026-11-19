---
id: model/pythia-160m
type: model
slug: pythia-160m
schema_version: 1.1
title: pythia-160m
organization: organization/eleutherai
release_date: '2023-02-08'
release_year: 2023
release_month: 2
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 0.21
total_parameters: 212654688
context_window_tokens: 2048
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 2048
  vocabulary_size: 50304
  activation_function: gelu
  normalization: LayerNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 2926497
  likes: 67
  last_modified: '2023-07-09'
  observed_at: '2026-11-19'
  source: https://huggingface.co/EleutherAI/pythia-160m
aliases:
- EleutherAI/pythia-160m
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- gpt-neox
- dense
- mha
updated: '2026-11-19'
sources:
- url: https://huggingface.co/EleutherAI/pythia-160m
  title: EleutherAI/pythia-160m — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/EleutherAI/pythia-160m/blob/main/config.json
  title: EleutherAI/pythia-160m — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2304.01373
  title: arXiv:2304.01373
  trust: 0.95
- url: https://arxiv.org/abs/2101.00027
  title: arXiv:2101.00027
  trust: 0.95
- url: https://arxiv.org/abs/2201.07311
  title: arXiv:2201.07311
  trust: 0.95
---


