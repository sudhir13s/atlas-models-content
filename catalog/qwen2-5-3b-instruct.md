---
id: model/qwen2-5-3b-instruct
type: model
slug: qwen2-5-3b-instruct
schema_version: 1.1
title: Qwen2.5-3B-Instruct
organization: organization/qwen
release_date: '2024-09-17'
release_year: 2024
release_month: 9
license_note: qwen-research
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 3.09
total_parameters: 3085938688
context_window_tokens: 32768
base_models:
- name: Qwen/Qwen2.5-3B
  address: https://huggingface.co/Qwen/Qwen2.5-3B
summary: Qwen2.5 3B Instruct is a language model from Qwen that reads text and writes text in reply. It is the instruction-tuned member of the Qwen2.5 series at the 3B size (3.09 billion parameters), which means the base model was trained further to follow instructions and hold a conversation. The card says the Qwen2.5 series ranges from 0.5 to 72 billion parameters.
what_sets_it_apart: Qwen states that, compared with the earlier Qwen2 series, Qwen2.5 has more knowledge and improved coding and mathematics, follows instructions better, generates long texts of over 8K tokens, understands structured data such as tables and produces structured outputs such as JSON. The card also states support for a context of up to 128K tokens and for over 29 languages.
fingerprint:
  attention_type: GQA
  num_layers: 36
  attention_heads: 16
  kv_heads: 2
  head_size: 128
  hidden_size: 2048
  intermediate_size: 11008
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
  downloads_30d: 1861063
  likes: 644
  last_modified: '2024-09-25'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2.5-3B-Instruct
aliases:
- Qwen/Qwen2.5-3B-Instruct
- qwen2.5-3b-instruct
lineage:
  parents: []
  children: []
quotes:
  summary: This repo contains the instruction-tuned 3B Qwen2.5 model
  what_sets_it_apart: Significantly **more knowledge** and has greatly improved capabilities in **coding** and **mathematics**, thanks to our specialized expert models in these domains.
tags:
- llm
- text-generation
- qwen2
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-3B-Instruct
  title: Qwen/Qwen2.5-3B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-3B-Instruct/blob/main/config.json
  title: Qwen/Qwen2.5-3B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2407.10671
  title: arXiv:2407.10671
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen2/modeling_qwen2.py
  title: 'transformers/models/qwen2/modeling_qwen2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Qwen2.5 3B Instruct is a language model from Qwen that reads text and writes text in reply. It is the instruction-tuned member of the Qwen2.5 series at the 3B size (3.09 billion parameters), which means the base model was trained further to follow instructions and hold a conversation. The card says the Qwen2.5 series ranges from 0.5 to 72 billion parameters.

## What Sets It Apart

Qwen states that, compared with the earlier Qwen2 series, Qwen2.5 has more knowledge and improved coding and mathematics, follows instructions better, generates long texts of over 8K tokens, understands structured data such as tables and produces structured outputs such as JSON. The card also states support for a context of up to 128K tokens and for over 29 languages.
