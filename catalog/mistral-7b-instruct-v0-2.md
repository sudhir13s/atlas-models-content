---
id: model/mistral-7b-instruct-v0-2
type: model
slug: mistral-7b-instruct-v0-2
schema_version: 1.1
title: Mistral-7B-Instruct-v0.2
organization: organization/mistral-ai
release_date: '2023-12-11'
release_year: 2023
release_month: 12
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 7.24
total_parameters: 7241732096
context_window_tokens: 32768
summary: Mistral-7B-Instruct-v0.2 is a large language model from Mistral AI. Its card says it is an instruct fine-tuned version of Mistral-7B-v0.2, meaning the base model was trained further to follow instructions given as chat messages.
what_sets_it_apart: 'The card lists three changes in the Mistral-7B-v0.2 base model compared with v0.1: a 32k context window instead of 8k, a rope-theta setting of 1e6 (a parameter of the rotary position encoding), and no sliding-window attention. It points to Mistral''s paper and release blog post for full details.'
running_it: The card says the prompt should be surrounded by [INST] and [/INST] tokens. The first instruction begins with the begin-of-sentence id, later ones do not, and the assistant's reply ends with the end-of-sentence token. The same format is available through the chat template in the Transformers library.
limitations: Mistral calls the model a quick demonstration that the base model can be easily fine-tuned, and says it has no moderation mechanisms, so it is not yet suited to deployments that need moderated outputs.
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 14336
  context_window_tokens: 32768
  vocabulary_size: 32000
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
  downloads_30d: 1054362
  likes: 3283
  last_modified: '2025-07-24'
  observed_at: '2026-11-20'
  source: https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2
aliases:
- mistralai/Mistral-7B-Instruct-v0.2
- mistral-7b-instruct-v0.2
lineage:
  parents: []
  children: []
quotes:
  summary: The Mistral-7B-Instruct-v0.2 Large Language Model (LLM) is an instruct fine-tuned version of the Mistral-7B-v0.2.
  what_sets_it_apart: 32k context window (vs 8k context in v0.1)
  running_it: your prompt should be surrounded by `[INST]` and `[/INST]` tokens.
  limitations: It does not have any moderation mechanisms.
tags:
- llm
- text-generation
- mistral
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2
  title: mistralai/Mistral-7B-Instruct-v0.2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2/blob/main/config.json
  title: mistralai/Mistral-7B-Instruct-v0.2 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2310.06825
  title: arXiv:2310.06825
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/mistral/modeling_mistral.py
  title: 'transformers/models/mistral/modeling_mistral.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Mistral-7B-Instruct-v0.2 is a large language model from Mistral AI. Its card says it is an instruct fine-tuned version of Mistral-7B-v0.2, meaning the base model was trained further to follow instructions given as chat messages.

## What Sets It Apart

The card lists three changes in the Mistral-7B-v0.2 base model compared with v0.1: a 32k context window instead of 8k, a rope-theta setting of 1e6 (a parameter of the rotary position encoding), and no sliding-window attention. It points to Mistral's paper and release blog post for full details.

## Running It

The card says the prompt should be surrounded by [INST] and [/INST] tokens. The first instruction begins with the begin-of-sentence id, later ones do not, and the assistant's reply ends with the end-of-sentence token. The same format is available through the chat template in the Transformers library.

## Limitations

Mistral calls the model a quick demonstration that the base model can be easily fine-tuned, and says it has no moderation mechanisms, so it is not yet suited to deployments that need moderated outputs.
