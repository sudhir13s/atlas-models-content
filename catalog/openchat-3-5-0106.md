---
id: model/openchat-3-5-0106
type: model
slug: openchat-3-5-0106
schema_version: 1.1
title: openchat-3.5-0106
organization: organization/openchat
release_date: '2024-01-07'
release_year: 2024
release_month: 1
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 7.24
total_parameters: 7241748480
context_window_tokens: 8192
summary: OpenChat 3.5 0106 is a chat language model from the OpenChat project, fine-tuned from Mistral AI's Mistral-7B-v0.1. The card lists a size of 7B and a context of 8192 tokens, and its headline describes the project as advancing open-source language models with mixed-quality data.
intended_use: The card recommends serving it with the OpenChat package's OpenAI-compatible API server, which it says can run on a consumer GPU with 24GB of memory. It describes two modes, one for coding and general use and one for mathematical reasoning, and experimental support for evaluator and feedback uses.
what_sets_it_apart: The card tags the training method as C-RLFT and describes two modes, Coding plus Generalist and Mathematical Reasoning. It also lists experimental support for evaluator and feedback capabilities.
limitations: The card says the model is bound by the limitations of its foundation models, which may affect complex reasoning, mathematical and arithmetic tasks, and programming. It may also generate information that does not exist, so users should verify critical information.
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 14336
  context_window_tokens: 8192
  vocabulary_size: 32002
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 12413
  likes: 357
  last_modified: '2024-05-18'
  observed_at: '2026-11-20'
  source: https://huggingface.co/openchat/openchat-3.5-0106
aliases:
- openchat/openchat-3.5-0106
- openchat-3.5-0106
lineage:
  parents:
  - model/mistral-7b-v0-1
  children: []
quotes:
  summary: Advancing Open-source Language Models with Mixed-Quality Data
  intended_use: can run on a consumer GPU with 24GB RAM
  what_sets_it_apart: '2 Modes: Coding + Generalist, Mathematical Reasoning'
  limitations: OpenChat may sometimes generate information that does not exist or is not accurate
tags:
- llm
- text-generation
- mistral
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/openchat/openchat-3.5-0106
  title: openchat/openchat-3.5-0106 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openchat/openchat-3.5-0106/blob/main/config.json
  title: openchat/openchat-3.5-0106 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2309.11235
  title: arXiv:2309.11235
  trust: 0.95
- url: https://arxiv.org/abs/2303.08774
  title: arXiv:2303.08774
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/mistral/modeling_mistral.py
  title: 'transformers/models/mistral/modeling_mistral.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

OpenChat 3.5 0106 is a chat language model from the OpenChat project, fine-tuned from Mistral AI's Mistral-7B-v0.1. The card lists a size of 7B and a context of 8192 tokens, and its headline describes the project as advancing open-source language models with mixed-quality data.

## Intended Use

The card recommends serving it with the OpenChat package's OpenAI-compatible API server, which it says can run on a consumer GPU with 24GB of memory. It describes two modes, one for coding and general use and one for mathematical reasoning, and experimental support for evaluator and feedback uses.

## What Sets It Apart

The card tags the training method as C-RLFT and describes two modes, Coding plus Generalist and Mathematical Reasoning. It also lists experimental support for evaluator and feedback capabilities.

## Limitations

The card says the model is bound by the limitations of its foundation models, which may affect complex reasoning, mathematical and arithmetic tasks, and programming. It may also generate information that does not exist, so users should verify critical information.
