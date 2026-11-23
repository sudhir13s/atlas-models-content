---
id: model/qwen2-5-1-5b
type: model
slug: qwen2-5-1-5b
schema_version: 1.1
title: Qwen2.5-1.5B
organization: organization/qwen
release_date: '2024-09-15'
release_year: 2024
release_month: 9
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 1.54
total_parameters: 1543714304
context_window_tokens: 131072
summary: Qwen2.5-1.5B is the base, pretrained 1.5B-parameter model of the Qwen2.5 series from Qwen, a causal language model that continues text. The card lists 1.54B parameters, 28 layers and a full context length of 32,768 tokens. The Qwen2.5 series ranges from 0.5 to 72 billion parameters.
intended_use: The card says base language models are not recommended for conversations. Instead it suggests applying post-training to this model, such as supervised fine-tuning (SFT), reinforcement learning from human feedback (RLHF) or continued pretraining.
what_sets_it_apart: Qwen states that, compared with the earlier Qwen2 series, Qwen2.5 has more knowledge and improved coding and mathematics, follows instructions better, generates long texts of over 8K tokens, understands structured data such as tables and produces structured outputs such as JSON. The series description lists long-context support up to 128K tokens and over 29 languages, while this size's feature list gives a full context length of 32,768 tokens.
running_it: The card advises using the latest version of the Transformers library, and says versions before 4.37.0 fail with a KeyError for qwen2.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 12
  kv_heads: 2
  head_size: 128
  hidden_size: 1536
  intermediate_size: 8960
  context_window_tokens: 131072
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
  downloads_30d: 497363
  likes: 249
  last_modified: '2024-10-08'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2.5-1.5B
variants:
- name: RedHatAI/Qwen2.5-1.5B-quantized.w8a8
  format: w8a8
  address: https://huggingface.co/RedHatAI/Qwen2.5-1.5B-quantized.w8a8
  publisher: RedHatAI
aliases:
- Qwen/Qwen2.5-1.5B
- qwen2.5-1.5b-quantized.w8a8
lineage:
  parents: []
  children:
  - model/qwen2-5-1-5b-instruct
quotes:
  summary: This repo contains the base 1.5B Qwen2.5 model
  intended_use: We do not recommend using base language models for conversations.
  what_sets_it_apart: Significantly **more knowledge** and has greatly improved capabilities in **coding** and **mathematics**, thanks to our specialized expert models in these domains.
  running_it: 'With `transformers<4.37.0`, you will encounter the following error:'
tags:
- llm
- text-generation
- qwen2
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-1.5B
  title: Qwen/Qwen2.5-1.5B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-1.5B/blob/main/config.json
  title: Qwen/Qwen2.5-1.5B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2407.10671
  title: arXiv:2407.10671
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen2/modeling_qwen2.py
  title: 'transformers/models/qwen2/modeling_qwen2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Qwen2.5-1.5B is the base, pretrained 1.5B-parameter model of the Qwen2.5 series from Qwen, a causal language model that continues text. The card lists 1.54B parameters, 28 layers and a full context length of 32,768 tokens. The Qwen2.5 series ranges from 0.5 to 72 billion parameters.

## Intended Use

The card says base language models are not recommended for conversations. Instead it suggests applying post-training to this model, such as supervised fine-tuning (SFT), reinforcement learning from human feedback (RLHF) or continued pretraining.

## What Sets It Apart

Qwen states that, compared with the earlier Qwen2 series, Qwen2.5 has more knowledge and improved coding and mathematics, follows instructions better, generates long texts of over 8K tokens, understands structured data such as tables and produces structured outputs such as JSON. The series description lists long-context support up to 128K tokens and over 29 languages, while this size's feature list gives a full context length of 32,768 tokens.

## Running It

The card advises using the latest version of the Transformers library, and says versions before 4.37.0 fail with a KeyError for qwen2.
