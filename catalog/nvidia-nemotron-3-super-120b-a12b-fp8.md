---
id: model/nvidia-nemotron-3-super-120b-a12b-fp8
type: model
slug: nvidia-nemotron-3-super-120b-a12b-fp8
schema_version: 1.1
title: NVIDIA-Nemotron-3-Super-120B-A12B-FP8
organization: organization/nvidia
release_date: '2026-03-10'
release_year: 2026
release_month: 3
license_note: nvidia-nemotron-open-model-license
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
total_parameters: 123611012096
context_window_tokens: 262144
fingerprint:
  attention_type: GQA
  num_layers: 88
  attention_heads: 32
  kv_heads: 2
  head_size: 128
  hidden_size: 4096
  intermediate_size: 2688
  context_window_tokens: 262144
  vocabulary_size: 131072
  activation_function: relu2
  normalization: LayerNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
moe_config:
  expert_count: 512
  active_experts: 22
  shared_expert: true
filters:
  size_category: xlarge
  is_quantized: true
  quant_format: modelopt
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 27809
  likes: 293
  last_modified: '2026-08-24'
  observed_at: '2026-11-19'
  source: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8
aliases:
- nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8
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
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8
  title: nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8/blob/main/config.json
  title: nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2512.20848
  title: arXiv:2512.20848
  trust: 0.95
- url: https://arxiv.org/abs/2512.20856
  title: arXiv:2512.20856
  trust: 0.95
---


