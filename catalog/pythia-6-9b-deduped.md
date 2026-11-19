---
id: model/pythia-6-9b-deduped
type: model
slug: pythia-6-9b-deduped
schema_version: 1.1
title: pythia-6.9b-deduped
organization: organization/eleutherai
release_date: '2023-02-25'
release_year: 2023
release_month: 2
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
context_window_tokens: 2048
fingerprint:
  attention_type: MHA
  num_layers: 32
  attention_heads: 32
  kv_heads: 32
  head_size: 128
  hidden_size: 4096
  intermediate_size: 16384
  context_window_tokens: 2048
  vocabulary_size: 50432
  activation_function: gelu
  normalization: LayerNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 6946
  likes: 7
  last_modified: '2023-06-08'
  observed_at: '2026-11-19'
  source: https://huggingface.co/EleutherAI/pythia-6.9b-deduped
aliases:
- EleutherAI/pythia-6.9b-deduped
- pythia-6.9b-deduped
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
- url: https://huggingface.co/EleutherAI/pythia-6.9b-deduped
  title: EleutherAI/pythia-6.9b-deduped — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/EleutherAI/pythia-6.9b-deduped/blob/main/config.json
  title: EleutherAI/pythia-6.9b-deduped — config.json
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


