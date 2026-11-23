---
id: model/internlm2-5-7b-chat
type: model
slug: internlm2-5-7b-chat
schema_version: 1.1
title: internlm2_5-7b-chat
organization: organization/internlm
release_date: '2024-06-27'
release_year: 2024
release_month: 6
license_note: other
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 7.74
total_parameters: 7737708544
context_window_tokens: 32768
summary: InternLM2.5-7B-Chat is a chat language model with 7 billion parameters from the InternLM project. The card says InternLM2.5 open-sources a 7 billion parameter base model together with this chat model, which is tailored for practical scenarios. It is a text model that reads a conversation and writes a reply.
what_sets_it_apart: 'The card lists three characteristics as the project''s own claims: strong reasoning on maths problems, a context window of 1 million tokens (the amount of text it can take in at once), and better tool use, including gathering information from more than 100 web pages through the MindSearch project. The card suggests trying LMDeploy for 1M-context inference.'
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 14336
  context_window_tokens: 32768
  vocabulary_size: 92544
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
  downloads_30d: 21574
  likes: 202
  last_modified: '2025-03-13'
  observed_at: '2026-11-20'
  source: https://huggingface.co/internlm/internlm2_5-7b-chat
aliases:
- internlm/internlm2_5-7b-chat
lineage:
  parents: []
  children: []
quotes:
  summary: InternLM2.5 has open-sourced a 7 billion parameter base model and a chat model tailored for practical scenarios.
  what_sets_it_apart: InternLM2.5 supports gathering information from more than 100 web pages
tags:
- llm
- text-generation
- internlm2
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/internlm/internlm2_5-7b-chat
  title: internlm/internlm2_5-7b-chat — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/internlm/internlm2_5-7b-chat/blob/main/config.json
  title: internlm/internlm2_5-7b-chat — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2403.17297
  title: arXiv:2403.17297
  trust: 0.95
- url: https://huggingface.co/internlm/internlm2_5-7b-chat/blob/main/modeling_internlm2.py
  title: 'internlm/internlm2_5-7b-chat: modeling_internlm2.py (official implementation)'
  trust: 0.9
---

## Summary

InternLM2.5-7B-Chat is a chat language model with 7 billion parameters from the InternLM project. The card says InternLM2.5 open-sources a 7 billion parameter base model together with this chat model, which is tailored for practical scenarios. It is a text model that reads a conversation and writes a reply.

## What Sets It Apart

The card lists three characteristics as the project's own claims: strong reasoning on maths problems, a context window of 1 million tokens (the amount of text it can take in at once), and better tool use, including gathering information from more than 100 web pages through the MindSearch project. The card suggests trying LMDeploy for 1M-context inference.
