---
id: model/gemma-4-e4b-it
type: model
slug: gemma-4-e4b-it
schema_version: 1.1
title: gemma-4-E4B-it
organization: organization/google-deepmind
release_date: '2026-03-02'
release_year: 2026
release_month: 3
license: license/apache-2-0
model_category: multimodal
tasks:
- task/any-to-any-models
parameters_active_b: 8.0
total_parameters: 7996156490
context_window_tokens: 131072
base_models:
- name: google/gemma-4-E4B
  address: https://huggingface.co/google/gemma-4-E4B
summary: Gemma 4 E4B IT is the instruction-tuned E4B model of Google DeepMind's Gemma 4 family of open models. It accepts text and image input, plus audio on this size, and writes text as its output. The card gives it 4.5 billion effective parameters (8 billion with embeddings), a 128K-token context window and support for over 140 languages.
intended_use: The card says Gemma 4 is well suited to text generation, coding and reasoning, and to agentic workflows and multimodal understanding. It describes the E2B and E4B sizes as aimed at mobile and edge devices, and all models as having native function calling for building agents.
what_sets_it_apart: The E in E4B stands for effective parameters. The card says the small models use Per-Layer Embeddings, which give each decoder layer its own small embedding for every token, so the large lookup tables add storage but little computation. It also describes an attention design that interleaves local sliding-window layers with global layers, configurable thinking modes, and native support for a system role.
fingerprint:
  attention_type: GQA
  num_layers: 42
  attention_heads: 8
  kv_heads: 2
  head_size: 256
  hidden_size: 2560
  intermediate_size: 10240
  context_window_tokens: 131072
  vocabulary_size: 262144
  normalization: RMSNorm
  positional_encoding: RoPE
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: any-to-any
  library: transformers
hub_signals:
  downloads_30d: 4326816
  likes: 1890
  last_modified: '2026-11-03'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/gemma-4-E4B-it
aliases:
- google/gemma-4-E4B-it
lineage:
  parents: []
  children: []
quotes:
  summary: Gemma 4 models are multimodal, handling text and image input (with audio supported on E2B, E4B, and 12B) and generating text output.
  intended_use: Gemma 4 is well-suited for tasks like text generation, coding, and reasoning.
  what_sets_it_apart: The "E" in E2B and E4B stands for "effective" parameters.
tags:
- any-to-any
- gemma4
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/gemma-4-E4B-it
  title: google/gemma-4-E4B-it — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/gemma-4-E4B-it/blob/main/config.json
  title: google/gemma-4-E4B-it — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2607.02770
  title: arXiv:2607.02770
  trust: 0.95
---

## Summary

Gemma 4 E4B IT is the instruction-tuned E4B model of Google DeepMind's Gemma 4 family of open models. It accepts text and image input, plus audio on this size, and writes text as its output. The card gives it 4.5 billion effective parameters (8 billion with embeddings), a 128K-token context window and support for over 140 languages.

## Intended Use

The card says Gemma 4 is well suited to text generation, coding and reasoning, and to agentic workflows and multimodal understanding. It describes the E2B and E4B sizes as aimed at mobile and edge devices, and all models as having native function calling for building agents.

## What Sets It Apart

The E in E4B stands for effective parameters. The card says the small models use Per-Layer Embeddings, which give each decoder layer its own small embedding for every token, so the large lookup tables add storage but little computation. It also describes an attention design that interleaves local sliding-window layers with global layers, configurable thinking modes, and native support for a system role.
