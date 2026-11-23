---
id: model/qwen3-32b
type: model
slug: qwen3-32b
schema_version: 1.1
title: Qwen3-32B
organization: organization/qwen
release_date: '2025-04-27'
release_year: 2025
release_month: 4
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 32.76
total_parameters: 32762123264
context_window_tokens: 40960
summary: Qwen3-32B is a language model from Qwen that reads text and writes text in reply. It belongs to Qwen3, a generation the card describes as a suite of dense and mixture-of-experts (MoE) models, and the card lists it with 32.8 billion parameters. Its context length is 32,768 tokens natively and 131,072 tokens with YaRN, a technique for extending the length a model handles.
what_sets_it_apart: Qwen says Qwen3 supports switching between a thinking mode, for complex reasoning, maths and coding, and a non-thinking mode, for efficient general dialogue, inside one model. The card also states support for more than 100 languages and dialects, and tool use in both modes.
running_it: The card says to use Temperature=0.6, TopP=0.95, TopK=20 and MinP=0 in thinking mode, and Temperature=0.7, TopP=0.8, TopK=20 and MinP=0 in non-thinking mode, and not to use greedy decoding. For inputs beyond 32,768 tokens it describes enabling YaRN, and warns that static YaRN may affect performance on shorter texts, so it should be added only when long contexts are needed.
fingerprint:
  attention_type: GQA
  num_layers: 64
  attention_heads: 64
  kv_heads: 8
  head_size: 128
  hidden_size: 5120
  intermediate_size: 25600
  context_window_tokens: 40960
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: large
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 4000684
  likes: 797
  last_modified: '2025-07-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-32B
variants:
- name: Qwen/Qwen3-32B-AWQ
  format: awq
  address: https://huggingface.co/Qwen/Qwen3-32B-AWQ
  publisher: Qwen
aliases:
- Qwen/Qwen3-32B
- qwen3-32b-awq
lineage:
  parents: []
  children: []
quotes:
  summary: Qwen3 is the latest generation of large language models in Qwen series, offering a comprehensive suite of dense and mixture-of-experts (MoE) models.
  what_sets_it_apart: Uniquely support of seamless switching between thinking mode
  running_it: We advise adding the `rope_scaling` configuration only when processing long contexts is required.
tags:
- llm
- text-generation
- qwen3
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-32B
  title: Qwen/Qwen3-32B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json
  title: Qwen/Qwen3-32B — config.json
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

Qwen3-32B is a language model from Qwen that reads text and writes text in reply. It belongs to Qwen3, a generation the card describes as a suite of dense and mixture-of-experts (MoE) models, and the card lists it with 32.8 billion parameters. Its context length is 32,768 tokens natively and 131,072 tokens with YaRN, a technique for extending the length a model handles.

## What Sets It Apart

Qwen says Qwen3 supports switching between a thinking mode, for complex reasoning, maths and coding, and a non-thinking mode, for efficient general dialogue, inside one model. The card also states support for more than 100 languages and dialects, and tool use in both modes.

## Running It

The card says to use Temperature=0.6, TopP=0.95, TopK=20 and MinP=0 in thinking mode, and Temperature=0.7, TopP=0.8, TopK=20 and MinP=0 in non-thinking mode, and not to use greedy decoding. For inputs beyond 32,768 tokens it describes enabling YaRN, and warns that static YaRN may affect performance on shorter texts, so it should be added only when long contexts are needed.
