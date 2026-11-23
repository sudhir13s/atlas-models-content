---
id: model/qwen3-30b-a3b
type: model
slug: qwen3-30b-a3b
schema_version: 1.1
title: Qwen3-30B-A3B
organization: organization/qwen
release_date: '2025-04-27'
release_year: 2025
release_month: 4
license: license/apache-2-0
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
total_parameters: 30532122624
context_window_tokens: 32768
base_models:
- name: Qwen/Qwen3-30B-A3B-Base
  address: https://huggingface.co/Qwen/Qwen3-30B-A3B-Base
summary: 'Qwen3-30B-A3B is a language model from Qwen that reads text and writes text in reply. It is a mixture-of-experts model, meaning only some of its parameters are used for each token: the card lists 30.5B parameters in total with 3.3B activated, from 128 experts of which 8 are active. It is part of the Qwen3 series and supports a context of 32,768 tokens natively and 131,072 tokens with YaRN.'
what_sets_it_apart: Qwen states that a single model can switch between a thinking mode, for complex logical reasoning, math and coding, and a non-thinking mode for efficient general dialogue. The card describes a switch, enable_thinking, and also soft switches, /think and /no_think, that change the mode from one turn to the next. It also lists improved reasoning, agent abilities and support for more than 100 languages and dialects.
running_it: For thinking mode the card recommends temperature 0.6, top-p 0.95, top-k 20 and min-p 0, and says not to use greedy decoding because it can cause endless repetition. For non-thinking mode it suggests temperature 0.7 and top-p 0.8. The config.json is set for 40,960 positions, and the card advises enabling YaRN only for longer inputs because static YaRN may affect performance on shorter texts.
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 32
  kv_heads: 4
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
moe_config:
  expert_count: 128
  active_experts: 8
filters:
  size_category: large
  is_quantized: false
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 1119152
  likes: 986
  last_modified: '2025-07-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-30B-A3B
aliases:
- Qwen/Qwen3-30B-A3B
lineage:
  parents: []
  children: []
quotes:
  summary: 'Number of Parameters: 30.5B in total and 3.3B activated'
  what_sets_it_apart: Uniquely support of seamless switching between thinking mode
  running_it: For thinking mode (`enable_thinking=True`), use `Temperature=0.6`, `TopP=0.95`, `TopK=20`, and `MinP=0`.
locked:
- context_window_tokens
- fingerprint.context_window_tokens
tags:
- llm
- text-generation
- qwen3-moe
- moe
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-30B-A3B
  title: Qwen/Qwen3-30B-A3B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-30B-A3B/blob/main/config.json
  title: Qwen/Qwen3-30B-A3B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2309.00071
  title: arXiv:2309.00071
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3_moe/modeling_qwen3_moe.py
  title: 'transformers/models/qwen3_moe/modeling_qwen3_moe.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Qwen3-30B-A3B is a language model from Qwen that reads text and writes text in reply. It is a mixture-of-experts model, meaning only some of its parameters are used for each token: the card lists 30.5B parameters in total with 3.3B activated, from 128 experts of which 8 are active. It is part of the Qwen3 series and supports a context of 32,768 tokens natively and 131,072 tokens with YaRN.

## What Sets It Apart

Qwen states that a single model can switch between a thinking mode, for complex logical reasoning, math and coding, and a non-thinking mode for efficient general dialogue. The card describes a switch, enable_thinking, and also soft switches, /think and /no_think, that change the mode from one turn to the next. It also lists improved reasoning, agent abilities and support for more than 100 languages and dialects.

## Running It

For thinking mode the card recommends temperature 0.6, top-p 0.95, top-k 20 and min-p 0, and says not to use greedy decoding because it can cause endless repetition. For non-thinking mode it suggests temperature 0.7 and top-p 0.8. The config.json is set for 40,960 positions, and the card advises enabling YaRN only for longer inputs because static YaRN may affect performance on shorter texts.
