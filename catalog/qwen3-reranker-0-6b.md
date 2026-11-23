---
id: model/qwen3-reranker-0-6b
type: model
slug: qwen3-reranker-0-6b
schema_version: 1.1
title: Qwen3-Reranker-0.6B
organization: organization/qwen
release_date: '2025-05-29'
release_year: 2025
release_month: 5
license: license/apache-2-0
model_category: retrieval
tasks:
- task/reranking-models
parameters_active_b: 0.6
total_parameters: 595776512
context_window_tokens: 40960
base_models:
- name: Qwen/Qwen3-0.6B-Base
  address: https://huggingface.co/Qwen/Qwen3-0.6B-Base
summary: 'Qwen3-Reranker-0.6B is a text reranking model from Qwen. A reranker is the second step of a search system: it scores how relevant each candidate document is to a query so the candidates can be put in order. The card lists 0.6 billion parameters, support for more than 100 languages including programming languages, and a 32k context length.'
intended_use: The card says the Qwen3 Embedding series, to which this reranker belongs, is designed for text embedding and ranking tasks such as text retrieval, code retrieval, text classification, text clustering and bitext mining. It adds that the embedding and reranking models can be combined.
what_sets_it_apart: 'The card says the reranker is instruction-aware: users can supply their own instruction to tailor it to a task, language or scenario. Qwen reports that in its own evaluation, using instructions typically gives a 1% to 5% improvement for most downstream tasks compared with not using them.'
running_it: The card recommends writing task-specific instructions and, in multilingual settings, writing them in English, because most instructions used during training were in English. The default prompt asks the model to retrieve relevant passages that answer a web search query.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 16
  kv_heads: 8
  head_size: 128
  hidden_size: 1024
  intermediate_size: 3072
  context_window_tokens: 40960
  vocabulary_size: 151669
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-ranking
  library: transformers
hub_signals:
  downloads_30d: 1182124
  likes: 441
  last_modified: '2026-04-16'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Qwen/Qwen3-Reranker-0.6B
aliases:
- Qwen/Qwen3-Reranker-0.6B
lineage:
  parents: []
  children: []
quotes:
  summary: 'Model Type: Text Reranking'
  intended_use: including text retrieval, code retrieval, text classification, text clustering, and bitext mining.
  what_sets_it_apart: using instructions (instruct) typically yields an improvement of 1% to 5% compared to not using them.
  running_it: In multilingual contexts, we also advise users to write their instructions in English, as most instructions utilized during the model training process were originally written in English.
tags:
- text-ranking
- qwen3
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-Reranker-0.6B
  title: Qwen/Qwen3-Reranker-0.6B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-Reranker-0.6B/blob/main/config.json
  title: Qwen/Qwen3-Reranker-0.6B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2506.05176
  title: arXiv:2506.05176
  trust: 0.95
---

## Summary

Qwen3-Reranker-0.6B is a text reranking model from Qwen. A reranker is the second step of a search system: it scores how relevant each candidate document is to a query so the candidates can be put in order. The card lists 0.6 billion parameters, support for more than 100 languages including programming languages, and a 32k context length.

## Intended Use

The card says the Qwen3 Embedding series, to which this reranker belongs, is designed for text embedding and ranking tasks such as text retrieval, code retrieval, text classification, text clustering and bitext mining. It adds that the embedding and reranking models can be combined.

## What Sets It Apart

The card says the reranker is instruction-aware: users can supply their own instruction to tailor it to a task, language or scenario. Qwen reports that in its own evaluation, using instructions typically gives a 1% to 5% improvement for most downstream tasks compared with not using them.

## Running It

The card recommends writing task-specific instructions and, in multilingual settings, writing them in English, because most instructions used during training were in English. The default prompt asks the model to retrieve relevant passages that answer a web search query.
