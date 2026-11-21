---
id: model/llava-video-7b-qwen2
type: model
slug: llava-video-7b-qwen2
schema_version: 1.1
title: LLaVA-Video-7B-Qwen2
organization: organization/lmms-lab
release_date: '2024-09-02'
release_year: 2024
release_month: 9
license: license/apache-2-0
model_category: vision
tasks:
- task/video-understanding
parameters_active_b: 8.03
total_parameters: 8030348832
context_window_tokens: 32768
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 28
  kv_heads: 4
  head_size: 128
  hidden_size: 3584
  intermediate_size: 18944
  context_window_tokens: 32768
  vocabulary_size: 152064
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: video-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 12134
  likes: 127
  last_modified: '2024-10-25'
  observed_at: '2026-11-21'
  source: https://huggingface.co/lmms-lab/LLaVA-Video-7B-Qwen2
aliases:
- lmms-lab/LLaVA-Video-7B-Qwen2
lineage:
  parents: []
  children: []
tags:
- video-text-to-text
- llava
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/lmms-lab/LLaVA-Video-7B-Qwen2
  title: lmms-lab/LLaVA-Video-7B-Qwen2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/lmms-lab/LLaVA-Video-7B-Qwen2/blob/main/config.json
  title: lmms-lab/LLaVA-Video-7B-Qwen2 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2410.02713
  title: arXiv:2410.02713
  trust: 0.95
---


