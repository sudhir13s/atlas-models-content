---
id: model/qwen3-8b-base
type: model
slug: qwen3-8b-base
schema_version: 1.1
title: Qwen3-8B-Base
organization: organization/qwen
release_date: '2025-04-28'
release_year: 2025
release_month: 4
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 8.19
total_parameters: 8190735360
context_window_tokens: 32768
summary: Qwen3-8B-Base is the pre-trained base model of the 8-billion-parameter Qwen3 language model from Qwen, before any instruction tuning. A base model learns to continue text and is meant to be adapted further. The card gives it 8.2 billion parameters, 36 layers and a context length of 32,768 tokens.
what_sets_it_apart: 'The card says Qwen3 was pre-trained on 36 trillion tokens across 119 languages, which it describes as triple the language coverage of Qwen2.5. It describes three pre-training stages: broad language modeling and general knowledge, then reasoning skills such as STEM, coding and logic, then long-context comprehension with sequences extended to 32k tokens.'
fingerprint:
  attention_type: GQA
  num_layers: 36
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 12288
  context_window_tokens: 32768
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
  downloads_30d: 630673
  likes: 152
  last_modified: '2025-05-21'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-8B-Base
aliases:
- Qwen/Qwen3-8B-Base
lineage:
  parents: []
  children:
  - model/qwen3-8b
quotes:
  summary: Qwen3 is the latest generation of large language models in Qwen series, offering a comprehensive suite of dense and mixture-of-experts (MoE) models.
  what_sets_it_apart: Stage 1 focuses on broad language modeling and general knowledge acquisition, Stage 2 improves reasoning skills like STEM, coding, and logical reasoning, and Stage 3 enhances long-context comprehension by extending training sequence lengths up to 32k tokens.
tags:
- llm
- text-generation
- qwen3
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-8B-Base
  title: Qwen/Qwen3-8B-Base — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-8B-Base/blob/main/config.json
  title: Qwen/Qwen3-8B-Base — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'transformers/models/qwen3/modeling_qwen3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Qwen3-8B-Base is the pre-trained base model of the 8-billion-parameter Qwen3 language model from Qwen, before any instruction tuning. A base model learns to continue text and is meant to be adapted further. The card gives it 8.2 billion parameters, 36 layers and a context length of 32,768 tokens.

## What Sets It Apart

The card says Qwen3 was pre-trained on 36 trillion tokens across 119 languages, which it describes as triple the language coverage of Qwen2.5. It describes three pre-training stages: broad language modeling and general knowledge, then reasoning skills such as STEM, coding and logic, then long-context comprehension with sequences extended to 32k tokens.
