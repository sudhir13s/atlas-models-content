---
id: model/mistral-7b-v0-1
type: model
slug: mistral-7b-v0-1
schema_version: 1.1
title: Mistral-7B-v0.1
organization: organization/mistral-ai
release_date: '2023-09-20'
release_year: 2023
release_month: 9
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 7.24
total_parameters: 7241732096
context_window_tokens: 32768
summary: Mistral-7B-v0.1 is a language model from Mistral AI with 7 billion parameters. The card calls it a pretrained generative text model and a pretrained base model, and says it has no moderation mechanisms. It is gated on Hugging Face, and the card says it needs Transformers 4.34.0 or newer.
what_sets_it_apart: The card lists a transformer with Grouped-Query Attention, in which several query heads share key and value heads, Sliding-Window Attention, in which each token looks only at a window of recent tokens, and a byte-fallback BPE tokenizer, which can fall back to raw bytes for text it has no token for. It also states that the model outperforms Llama 2 13B on all benchmarks the Mistral team tested.
running_it: The card says to use a stable version of Transformers, 4.34.0 or newer, to avoid a KeyError on the name mistral and a meta-tensor copy error.
limitations: The card says that, being a pretrained base model, it does not have any moderation mechanisms.
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
  downloads_30d: 422765
  likes: 4234
  last_modified: '2025-07-24'
  observed_at: '2026-11-20'
  source: https://huggingface.co/mistralai/Mistral-7B-v0.1
aliases:
- mistralai/Mistral-7B-v0.1
- mistral-7b-v0.1
lineage:
  parents: []
  children:
  - model/openchat-3-5-0106
  - model/zephyr-7b-beta
quotes:
  summary: is a pretrained generative text model with 7 billion parameters
  what_sets_it_apart: 'Mistral-7B-v0.1 is a transformer model, with the following architecture choices:'
  running_it: Ensure you are utilizing a stable version of Transformers, 4.34.0 or newer.
  limitations: does not have any moderation mechanisms
tags:
- llm
- text-generation
- mistral
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/mistralai/Mistral-7B-v0.1
  title: mistralai/Mistral-7B-v0.1 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/mistralai/Mistral-7B-v0.1/blob/main/config.json
  title: mistralai/Mistral-7B-v0.1 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2310.06825
  title: arXiv:2310.06825
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/mistral/modeling_mistral.py
  title: 'transformers/models/mistral/modeling_mistral.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Mistral-7B-v0.1 is a language model from Mistral AI with 7 billion parameters. The card calls it a pretrained generative text model and a pretrained base model, and says it has no moderation mechanisms. It is gated on Hugging Face, and the card says it needs Transformers 4.34.0 or newer.

## What Sets It Apart

The card lists a transformer with Grouped-Query Attention, in which several query heads share key and value heads, Sliding-Window Attention, in which each token looks only at a window of recent tokens, and a byte-fallback BPE tokenizer, which can fall back to raw bytes for text it has no token for. It also states that the model outperforms Llama 2 13B on all benchmarks the Mistral team tested.

## Running It

The card says to use a stable version of Transformers, 4.34.0 or newer, to avoid a KeyError on the name mistral and a meta-tensor copy error.

## Limitations

The card says that, being a pretrained base model, it does not have any moderation mechanisms.
