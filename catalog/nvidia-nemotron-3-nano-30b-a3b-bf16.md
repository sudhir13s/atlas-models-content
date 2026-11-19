---
id: model/nvidia-nemotron-3-nano-30b-a3b-bf16
type: model
slug: nvidia-nemotron-3-nano-30b-a3b-bf16
schema_version: 1.1
title: NVIDIA-Nemotron-3-Nano-30B-A3B-BF16
organization: organization/nvidia
release_date: '2025-12-04'
release_year: 2025
release_month: 12
license_note: nvidia-nemotron-open-model-license
model_category: language
total_parameters: 31577937344
context_window_tokens: 262144
fingerprint:
  attention_type: GQA
  num_layers: 52
  attention_heads: 32
  kv_heads: 2
  head_size: 128
  hidden_size: 2688
  intermediate_size: 1856
  context_window_tokens: 262144
  vocabulary_size: 131072
  activation_function: relu2
  normalization: LayerNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
moe_config:
  expert_count: 128
  active_experts: 6
  shared_expert: true
filters:
  size_category: large
  is_quantized: false
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 1320546
  likes: 840
  last_modified: '2026-08-24'
  observed_at: '2026-11-19'
  source: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16
variants:
- name: nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-FP8
  format: fp8
  address: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-FP8
  publisher: nvidia
aliases:
- nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16
- nvidia-nemotron-3-nano-30b-a3b-fp8
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- nemotron-h
- moe
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16
  title: nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16/blob/main/config.json
  title: nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2512.20848
  title: arXiv:2512.20848
  trust: 0.95
- url: https://arxiv.org/abs/2512.20856
  title: arXiv:2512.20856
  trust: 0.95
---


