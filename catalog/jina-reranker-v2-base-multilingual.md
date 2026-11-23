---
id: model/jina-reranker-v2-base-multilingual
type: model
slug: jina-reranker-v2-base-multilingual
schema_version: 3.0
title: jina-reranker-v2-base-multilingual
organization: organization/jinaai
release_date: '2024-06-19'
license_note: cc-by-nc-4.0
model_category: retrieval
model_kind: transformer-language
tasks:
- task/reranking-models
parameters_active_b: 0.28
total_parameters: 278437633
context_window_tokens: 1024
summary: 'Jina Reranker v2 is a text reranking model from Jina AI. A reranker is the second step of a search system: it takes a query and a candidate document and scores how relevant the document is to the query, so the candidates can be put in order. The card calls it a cross-encoder, trained to rerank documents in multiple languages.'
intended_use: The card presents it as a reranking component for information retrieval systems. It says the repository is licensed for research and evaluation under CC-BY-NC-4.0 and points to Jina AI's API, AWS SageMaker or Azure Marketplace offerings for commercial use.
what_sets_it_apart: The card says the model handles inputs of up to 1024 tokens, and splits longer text into chunks with a sliding window and reranks each chunk separately. It adds that the model is equipped with a flash attention mechanism, which the card says improves its performance.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  vocabulary_size: 250002
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: post
  stack: encoder
  embedding_norm: true
  positional_encoding: Learned
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-ranking
  library: transformers
hub_signals:
  downloads_30d: 970236
  likes: 372
  last_modified: '2025-10-21'
  observed_at: '2026-11-21'
  source: https://huggingface.co/jinaai/jina-reranker-v2-base-multilingual
aliases:
- jinaai/jina-reranker-v2-base-multilingual
lineage:
  parents: []
  children: []
quotes:
  summary: It is a cross-encoder model that takes a query and a document pair as input and outputs a score indicating the relevance of the document to the query.
  intended_use: is a transformer-based model that has been fine-tuned for text reranking task, which is a crucial component in many information retrieval systems.
  what_sets_it_apart: To enable the model to handle long texts that exceed 1024 tokens, the model uses a sliding window approach to chunk the input text into smaller pieces and rerank each chunk separately.
locked:
- context_window_tokens
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/jinaai/jina-reranker-v2-base-multilingual
  title: jinaai/jina-reranker-v2-base-multilingual — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/jinaai/jina-reranker-v2-base-multilingual/blob/main/config.json
  title: jinaai/jina-reranker-v2-base-multilingual — config.json
  trust: 0.9
- url: https://huggingface.co/jinaai/jina-reranker-v2-base-multilingual/blob/main/modeling_xlm_roberta.py
  title: jinaai/jina-reranker-v2-base-multilingual — modeling_xlm_roberta.py
  trust: 0.9
- url: https://huggingface.co/jinaai/jina-reranker-v2-base-multilingual/blob/main/block.py
  title: jinaai/jina-reranker-v2-base-multilingual — block.py
  trust: 0.9
---


