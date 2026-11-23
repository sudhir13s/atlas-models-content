---
id: model/qwen3-reranker-8b
type: model
slug: qwen3-reranker-8b
schema_version: 3.0
title: Qwen3-Reranker-8B
organization: organization/qwen
release_date: '2025-05-29'
license: license/apache-2-0
model_category: retrieval
model_kind: transformer-language
tasks:
- task/reranking-models
parameters_active_b: 8.19
total_parameters: 8188548096
context_window_tokens: 32768
summary: Qwen3-Reranker-8B is a text reranking model from Qwen. A reranker takes a search query and a list of candidate documents and scores how well each document matches the query, so that the most relevant ones can be placed first. The card says it belongs to the Qwen3 Embedding series, is built on the dense Qwen3 foundation models, has 8B parameters, a 32k context length and support for more than 100 languages.
intended_use: The card says the Qwen3 Embedding series is designed for text embedding and ranking tasks, including text retrieval, code retrieval, text classification, text clustering and bitext mining. It adds that developers can combine the embedding and reranking models, with the embedding model finding candidates and the reranker ordering them.
what_sets_it_apart: 'The card says the reranking models are instruction aware: users can supply a custom instruction to suit a task, language or scenario, and its evaluation found that instructions typically improve results by 1% to 5% compared with none. The series comes in 0.6B, 4B and 8B sizes and inherits the multilingual and long-text abilities of its Qwen3 base.'
running_it: The card shows loading it as a cross-encoder in Sentence Transformers, which scores a query and a document together. For multilingual use it advises writing instructions in English, because most instructions used in training were in English.
fingerprint:
  attention_type: GQA
  num_layers: 36
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 12288
  vocabulary_size: 151669
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
  is_gated: false
  pipeline_tag: text-ranking
  library: transformers
hub_signals:
  downloads_30d: 60844
  likes: 298
  last_modified: '2026-04-16'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Qwen/Qwen3-Reranker-8B
aliases:
- Qwen/Qwen3-Reranker-8B
lineage:
  parents:
  - model/qwen3-8b-base
  children: []
quotes:
  summary: 'Model Type: Text Reranking'
  intended_use: specifically designed for text embedding and ranking tasks
  what_sets_it_apart: both embedding and reranking models support user-defined instructions to enhance performance for specific tasks, languages, or scenarios
  running_it: we also advise users to write their instructions in English
locked:
- context_window_tokens
tags:
- qwen3
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-Reranker-8B
  title: Qwen/Qwen3-Reranker-8B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-Reranker-8B/blob/main/config.json
  title: Qwen/Qwen3-Reranker-8B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2506.05176
  title: arXiv:2506.05176
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'transformers/models/qwen3/modeling_qwen3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


