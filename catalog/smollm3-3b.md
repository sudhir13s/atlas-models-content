---
id: model/smollm3-3b
type: model
slug: smollm3-3b
schema_version: 3.0
title: SmolLM3-3B
organization: organization/hugging-face
release_date: '2025-07-08'
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
- task/tool-calling-models
parameters_active_b: 3.08
total_parameters: 3075098624
context_window_tokens: 65536
base_models:
- name: HuggingFaceTB/SmolLM3-3B-Base
  address: https://huggingface.co/HuggingFaceTB/SmolLM3-3B-Base
summary: 'SmolLM3-3B is a 3-billion-parameter language model from Hugging Face''s HuggingFaceTB team. The card says it is a decoder-only transformer trained further from SmolLM3-3B-Base, and that it supports dual-mode reasoning, long context and six natively supported languages: English, French, Spanish, German, Italian and Portuguese.'
what_sets_it_apart: The card says it was pretrained on 11.2 trillion tokens with a staged curriculum of web, code, maths and reasoning data, then given 140 billion tokens of reasoning mid-training, supervised fine-tuning and Anchored Preference Optimization. It uses GQA and NoPE (positions without a positional encoding) in a 3 to 1 ratio, and is described as fully open, with weights, data mixture and training configs published.
running_it: The card recommends temperature=0.6 and top_p=0.95. Extended thinking is on by default and can be switched with the /think and /no_think flags in the system prompt or the enable_thinking setting. The config is set for 65,536 tokens, and longer inputs up to 128k use YaRN.
fingerprint:
  attention_type: GQA
  num_layers: 36
  attention_heads: 16
  kv_heads: 4
  head_size: 128
  hidden_size: 2048
  intermediate_size: 11008
  vocabulary_size: 128256
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 5000000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 161445
  likes: 1083
  last_modified: '2025-09-10'
  observed_at: '2026-11-20'
  source: https://huggingface.co/HuggingFaceTB/SmolLM3-3B
aliases:
- HuggingFaceTB/SmolLM3-3B
lineage:
  parents: []
  children: []
quotes:
  summary: SmolLM3 is a 3B parameter language model designed to push the boundaries of small models.
  what_sets_it_apart: The model is a decoder-only transformer using GQA and NoPE (with 3:1 ratio)
  running_it: We recommend setting `temperature=0.6` and `top_p=0.95` in the sampling parameters.
tags:
- smollm3
updated: '2026-11-23'
sources:
- url: https://huggingface.co/HuggingFaceTB/SmolLM3-3B
  title: HuggingFaceTB/SmolLM3-3B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/HuggingFaceTB/SmolLM3-3B/blob/main/config.json
  title: HuggingFaceTB/SmolLM3-3B — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/smollm3/modeling_smollm3.py
  title: 'transformers/models/smollm3/modeling_smollm3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


