---
id: model/qwen3-0-6b
type: model
slug: qwen3-0-6b
schema_version: 3.0
title: Qwen3-0.6B
organization: organization/qwen
release_date: '2025-04-27'
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
parameters_active_b: 0.75
total_parameters: 751632384
context_window_tokens: 32768
base_models:
- name: Qwen/Qwen3-0.6B-Base
  address: https://huggingface.co/Qwen/Qwen3-0.6B-Base
summary: Qwen3-0.6B is a language model from Qwen that reads text and writes text in reply. The card lists 0.6B parameters, 28 layers and a context length of 32,768 tokens, and gives the pretraining and post-training stages as its training. It is part of the Qwen3 series, which the card describes as a suite of dense and mixture-of-experts models.
what_sets_it_apart: Qwen states that a single model can switch between a thinking mode, for complex logical reasoning, math and coding, and a non-thinking mode for efficient general dialogue. The card also lists improved reasoning, human preference alignment, agent abilities that connect to external tools, and support for more than 100 languages and dialects.
running_it: The card says to use a recent transformers version, since versions older than 4.51.0 fail with a KeyError on the name qwen3. For thinking mode it recommends temperature 0.6, top-p 0.95, top-k 20 and min-p 0, and warns against greedy decoding because it can cause endless repetition. For non-thinking mode it suggests temperature 0.7 and top-p 0.8.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 16
  kv_heads: 8
  head_size: 128
  hidden_size: 1024
  intermediate_size: 3072
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 15392833
  likes: 1920
  last_modified: '2025-07-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-0.6B
variants:
- name: Qwen/Qwen3-0.6B-FP8
  format: fp8
  address: https://huggingface.co/Qwen/Qwen3-0.6B-FP8
  publisher: Qwen
aliases:
- Qwen/Qwen3-0.6B
- qwen3-0.6b
- qwen3-0.6b-fp8
lineage:
  parents: []
  children: []
quotes:
  summary: offering a comprehensive suite of dense and mixture-of-experts (MoE) models
  what_sets_it_apart: Uniquely support of seamless switching between thinking mode
  running_it: For thinking mode (`enable_thinking=True`), use `Temperature=0.6`, `TopP=0.95`, `TopK=20`, and `MinP=0`.
locked:
- context_window_tokens
tags:
- qwen3
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-0.6B
  title: Qwen/Qwen3-0.6B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-0.6B/blob/main/config.json
  title: Qwen/Qwen3-0.6B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'transformers/models/qwen3/modeling_qwen3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


