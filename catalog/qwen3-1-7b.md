---
id: model/qwen3-1-7b
type: model
slug: qwen3-1-7b
schema_version: 1.1
title: Qwen3-1.7B
organization: organization/qwen
release_date: '2025-04-27'
release_year: 2025
release_month: 4
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 2.03
total_parameters: 2031739904
context_window_tokens: 32768
base_models:
- name: Qwen/Qwen3-1.7B-Base
  address: https://huggingface.co/Qwen/Qwen3-1.7B-Base
summary: Qwen3-1.7B is a small language model from Qwen that reads text and writes text in reply. It belongs to Qwen3, a generation the card describes as a suite of dense and mixture-of-experts (MoE) models, and the card lists it with 1.7 billion parameters and a context length of 32,768 tokens. Its base model is Qwen3-1.7B-Base, which was trained further to give this post-trained version.
what_sets_it_apart: Qwen says Qwen3 supports switching between a thinking mode, for complex reasoning, maths and coding, and a non-thinking mode, for efficient general dialogue, inside one model. The card also states support for more than 100 languages and dialects, and tool use in both modes.
running_it: The card says to use Temperature=0.6, TopP=0.95, TopK=20 and MinP=0 in thinking mode, and Temperature=0.7, TopP=0.8, TopK=20 and MinP=0 in non-thinking mode, and not to use greedy decoding. It adds that thinking is switched with the enable_thinking setting, that Transformers 4.51.0 or later is needed, and that presence_penalty can be set to 1.5 if the model repeats itself endlessly.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 16
  kv_heads: 8
  head_size: 128
  hidden_size: 2048
  intermediate_size: 6144
  context_window_tokens: 32768
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
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 2673801
  likes: 633
  last_modified: '2025-07-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-1.7B
aliases:
- Qwen/Qwen3-1.7B
- qwen3-1.7b
lineage:
  parents: []
  children: []
quotes:
  summary: Qwen3 is the latest generation of large language models in Qwen series, offering a comprehensive suite of dense and mixture-of-experts (MoE) models.
  what_sets_it_apart: Uniquely support of seamless switching between thinking mode
  running_it: For thinking mode (`enable_thinking=True`), use `Temperature=0.6`, `TopP=0.95`, `TopK=20`, and `MinP=0`.
locked:
- context_window_tokens
- fingerprint.context_window_tokens
tags:
- llm
- text-generation
- qwen3
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-1.7B
  title: Qwen/Qwen3-1.7B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-1.7B/blob/main/config.json
  title: Qwen/Qwen3-1.7B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'transformers/models/qwen3/modeling_qwen3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Qwen3-1.7B is a small language model from Qwen that reads text and writes text in reply. It belongs to Qwen3, a generation the card describes as a suite of dense and mixture-of-experts (MoE) models, and the card lists it with 1.7 billion parameters and a context length of 32,768 tokens. Its base model is Qwen3-1.7B-Base, which was trained further to give this post-trained version.

## What Sets It Apart

Qwen says Qwen3 supports switching between a thinking mode, for complex reasoning, maths and coding, and a non-thinking mode, for efficient general dialogue, inside one model. The card also states support for more than 100 languages and dialects, and tool use in both modes.

## Running It

The card says to use Temperature=0.6, TopP=0.95, TopK=20 and MinP=0 in thinking mode, and Temperature=0.7, TopP=0.8, TopK=20 and MinP=0 in non-thinking mode, and not to use greedy decoding. It adds that thinking is switched with the enable_thinking setting, that Transformers 4.51.0 or later is needed, and that presence_penalty can be set to 1.5 if the model repeats itself endlessly.
