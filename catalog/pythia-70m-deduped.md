---
id: model/pythia-70m-deduped
type: model
slug: pythia-70m-deduped
schema_version: 1.1
title: pythia-70m-deduped
organization: organization/eleutherai
release_date: '2023-02-13'
release_year: 2023
release_month: 2
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 0.1
total_parameters: 95592496
context_window_tokens: 2048
fingerprint:
  attention_type: MHA
  num_layers: 6
  attention_heads: 8
  kv_heads: 8
  head_size: 64
  hidden_size: 512
  intermediate_size: 2048
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
  downloads_30d: 506459
  likes: 39
  last_modified: '2023-07-09'
  observed_at: '2026-11-20'
  source: https://huggingface.co/EleutherAI/pythia-70m-deduped
aliases:
- EleutherAI/pythia-70m-deduped
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- gpt-neox
- dense
- mha
updated: '2026-11-20'
sources:
- url: https://huggingface.co/EleutherAI/pythia-70m-deduped
  title: EleutherAI/pythia-70m-deduped — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/EleutherAI/pythia-70m-deduped/blob/main/config.json
  title: EleutherAI/pythia-70m-deduped — config.json
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


