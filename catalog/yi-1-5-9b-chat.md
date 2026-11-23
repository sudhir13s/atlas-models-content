---
id: model/yi-1-5-9b-chat
type: model
slug: yi-1-5-9b-chat
schema_version: 1.1
title: Yi-1.5-9B-Chat
organization: organization/01-ai
release_date: '2024-05-10'
release_year: 2024
release_month: 5
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 8.83
total_parameters: 8829407232
context_window_tokens: 4096
summary: Yi-1.5-9B-Chat is the 9B chat model of Yi-1.5, a language model family from 01.AI. The card describes Yi-1.5 as an upgraded version of Yi that was continuously pre-trained on a 500B-token corpus and fine-tuned on 3M diverse fine-tuning samples. It lists context lengths of 4K, 16K and 32K and 3.6T pre-trained tokens for the Yi-1.5 family.
what_sets_it_apart: The card says that, compared with Yi, Yi-1.5 delivers stronger performance in coding, math, reasoning and instruction following while keeping its strengths in language understanding, commonsense reasoning and reading comprehension. It describes this as the result of continued pre-training on Yi followed by fine-tuning.
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 32
  kv_heads: 4
  head_size: 128
  hidden_size: 4096
  intermediate_size: 11008
  context_window_tokens: 4096
  vocabulary_size: 64000
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 5000000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 18464
  likes: 149
  last_modified: '2024-06-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/01-ai/Yi-1.5-9B-Chat
aliases:
- 01-ai/Yi-1.5-9B-Chat
- yi-1.5-9b-chat
lineage:
  parents: []
  children: []
quotes:
  summary: Yi-1.5 is an upgraded version of Yi.
  what_sets_it_apart: Compared with Yi, Yi-1.5 delivers stronger performance in coding, math, reasoning, and instruction-following capability
tags:
- llm
- text-generation
- llama
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/01-ai/Yi-1.5-9B-Chat
  title: 01-ai/Yi-1.5-9B-Chat — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/01-ai/Yi-1.5-9B-Chat/blob/main/config.json
  title: 01-ai/Yi-1.5-9B-Chat — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2403.04652
  title: arXiv:2403.04652
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/modeling_llama.py
  title: 'transformers/models/llama/modeling_llama.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Yi-1.5-9B-Chat is the 9B chat model of Yi-1.5, a language model family from 01.AI. The card describes Yi-1.5 as an upgraded version of Yi that was continuously pre-trained on a 500B-token corpus and fine-tuned on 3M diverse fine-tuning samples. It lists context lengths of 4K, 16K and 32K and 3.6T pre-trained tokens for the Yi-1.5 family.

## What Sets It Apart

The card says that, compared with Yi, Yi-1.5 delivers stronger performance in coding, math, reasoning and instruction following while keeping its strengths in language understanding, commonsense reasoning and reading comprehension. It describes this as the result of continued pre-training on Yi followed by fine-tuning.
