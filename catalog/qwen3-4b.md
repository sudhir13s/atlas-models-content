---
id: model/qwen3-4b
type: model
slug: qwen3-4b
schema_version: 3.0
title: Qwen3-4B
organization: organization/qwen
release_date: '2025-04-27'
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
parameters_active_b: 4.02
total_parameters: 4022468096
context_window_tokens: 32768
base_models:
- name: Qwen/Qwen3-4B-Base
  address: https://huggingface.co/Qwen/Qwen3-4B-Base
summary: Qwen3-4B is a language model from Qwen that reads text and writes text in reply. The card lists 4.0B parameters and 36 layers, and says it supports a context of 32,768 tokens natively and 131,072 tokens with YaRN. It is part of the Qwen3 series, which the card describes as a suite of dense and mixture-of-experts models.
what_sets_it_apart: Qwen states that a single model can switch between a thinking mode, for complex logical reasoning, math and coding, and a non-thinking mode for efficient general dialogue. The card also lists improved reasoning, human preference alignment, agent abilities that connect to external tools, and support for more than 100 languages and dialects.
running_it: For thinking mode the card recommends temperature 0.6, top-p 0.95, top-k 20 and min-p 0, and says not to use greedy decoding because it can cause endless repetition. For non-thinking mode it suggests temperature 0.7 and top-p 0.8. It advises enabling YaRN only for longer inputs because static YaRN may affect performance on shorter texts.
fingerprint:
  attention_type: GQA
  num_layers: 36
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 2560
  intermediate_size: 9728
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 3114635
  likes: 804
  last_modified: '2025-07-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-4B
aliases:
- Qwen/Qwen3-4B
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
- url: https://huggingface.co/Qwen/Qwen3-4B
  title: Qwen/Qwen3-4B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-4B/blob/main/config.json
  title: Qwen/Qwen3-4B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2309.00071
  title: arXiv:2309.00071
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'transformers/models/qwen3/modeling_qwen3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


