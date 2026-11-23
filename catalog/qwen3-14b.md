---
id: model/qwen3-14b
type: model
slug: qwen3-14b
schema_version: 1.1
title: Qwen3-14B
organization: organization/qwen
release_date: '2025-04-27'
release_year: 2025
release_month: 4
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 14.77
total_parameters: 14768307200
context_window_tokens: 40960
base_models:
- name: Qwen/Qwen3-14B-Base
  address: https://huggingface.co/Qwen/Qwen3-14B-Base
summary: Qwen3-14B is a 14.8B-parameter language model from Qwen, part of the Qwen3 generation of dense and mixture-of-experts models. It was post-trained from Qwen/Qwen3-14B-Base. The card lists a context of 32,768 tokens natively and 131,072 tokens with YaRN, and support for 100+ languages and dialects.
what_sets_it_apart: Qwen says it supports switching between a thinking mode, for complex logic, math and coding, and a non-thinking mode, for efficient general dialogue, within a single model. It also claims better reasoning than the earlier QwQ and Qwen2.5 instruct models on mathematics, code generation and commonsense reasoning, and stronger agent abilities for working with external tools.
running_it: The card gives different sampling settings per mode. Thinking mode uses temperature 0.6, top_p 0.95, top_k 20 and min_p 0, and greedy decoding should not be used. Non-thinking mode, selected with enable_thinking=False, uses temperature 0.7, top_p 0.8, top_k 20 and min_p 0. It recommends an output length of 32,768 tokens for most queries. For inputs beyond 32,768 tokens it describes enabling YaRN, a technique for extending the length a model handles, but warns that its static form may affect shorter texts.
fingerprint:
  attention_type: GQA
  num_layers: 40
  attention_heads: 40
  kv_heads: 8
  head_size: 128
  hidden_size: 5120
  intermediate_size: 17408
  context_window_tokens: 40960
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
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
  downloads_30d: 13163564
  likes: 550
  last_modified: '2025-07-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-14B
variants:
- name: Qwen/Qwen3-14B-AWQ
  format: awq
  address: https://huggingface.co/Qwen/Qwen3-14B-AWQ
  publisher: Qwen
aliases:
- Qwen/Qwen3-14B
- qwen3-14b-awq
lineage:
  parents: []
  children: []
quotes:
  summary: Qwen3 is the latest generation of large language models in Qwen series, offering a comprehensive suite of dense and mixture-of-experts (MoE) models.
  what_sets_it_apart: Uniquely support of seamless switching between thinking mode
  running_it: '**DO NOT use greedy decoding**, as it can lead to performance degradation and endless repetitions.'
tags:
- llm
- text-generation
- qwen3
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-14B
  title: Qwen/Qwen3-14B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-14B/blob/main/config.json
  title: Qwen/Qwen3-14B — config.json
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

## Summary

Qwen3-14B is a 14.8B-parameter language model from Qwen, part of the Qwen3 generation of dense and mixture-of-experts models. It was post-trained from Qwen/Qwen3-14B-Base. The card lists a context of 32,768 tokens natively and 131,072 tokens with YaRN, and support for 100+ languages and dialects.

## What Sets It Apart

Qwen says it supports switching between a thinking mode, for complex logic, math and coding, and a non-thinking mode, for efficient general dialogue, within a single model. It also claims better reasoning than the earlier QwQ and Qwen2.5 instruct models on mathematics, code generation and commonsense reasoning, and stronger agent abilities for working with external tools.

## Running It

The card gives different sampling settings per mode. Thinking mode uses temperature 0.6, top_p 0.95, top_k 20 and min_p 0, and greedy decoding should not be used. Non-thinking mode, selected with enable_thinking=False, uses temperature 0.7, top_p 0.8, top_k 20 and min_p 0. It recommends an output length of 32,768 tokens for most queries. For inputs beyond 32,768 tokens it describes enabling YaRN, a technique for extending the length a model handles, but warns that its static form may affect shorter texts.
