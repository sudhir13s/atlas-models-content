---
id: model/bge-m3
type: model
slug: bge-m3
schema_version: 1.1
title: bge-m3
organization: organization/baai
release_date: '2024-01-27'
release_year: 2024
release_month: 1
license: license/mit
model_category: retrieval
tasks:
- task/embedding-models
parameters_active_b: 0.57
total_parameters: 567754752
context_window_tokens: 8192
embedding_dimension: 1024
base_models:
- name: BAAI/bge-m3-unsupervised
  address: https://huggingface.co/BAAI/bge-m3-unsupervised
- name: BAAI/bge-m3-retromae
  address: https://huggingface.co/BAAI/bge-m3-retromae
- name: FacebookAI/xlm-roberta-large
  address: https://huggingface.co/FacebookAI/xlm-roberta-large
summary: BGE-M3 is a text embedding model from the Beijing Academy of Artificial Intelligence (BAAI). It turns a sentence or a whole document into a list of numbers, called a vector, so that a search system can find texts with a similar meaning by comparing their vectors. Its model card states support for more than 100 languages and inputs of up to 8192 tokens.
intended_use: The model card presents it for the retrieval step of retrieval-augmented generation (RAG), where a system looks up relevant text before a language model writes an answer. For that step the card recommends hybrid retrieval, which combines meaning-based and keyword-based results, followed by a re-ranking model.
what_sets_it_apart: Its makers describe one model that returns three kinds of output at once. A dense output is a single vector per text, a sparse output is a weight for each token present in the text (the keyword-style matching that BM25 does), and a multi-vector output represents the text with several vectors, as ColBERT does. The card also states that it works across more than 100 languages and on inputs from a short sentence to a document of 8192 tokens.
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  intermediate_size: 4096
  context_window_tokens: 8192
  vocabulary_size: 250002
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: post
  stack: encoder
  positional_encoding: Learned
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: sentence-similarity
  library: sentence-transformers
hub_signals:
  downloads_30d: 33121367
  likes: 4028
  last_modified: '2024-07-03'
  observed_at: '2026-11-21'
  source: https://huggingface.co/BAAI/bge-m3
aliases:
- BAAI/bge-m3
lineage:
  parents: []
  children: []
quotes:
  summary: In this project, we introduce BGE-M3, which is distinguished for its versatility in Multi-Functionality, Multi-Linguality, and Multi-Granularity.
  intended_use: 'We recommend to use the following pipeline: hybrid retrieval + re-ranking.'
  what_sets_it_apart: 'It can simultaneously perform the three common retrieval functionalities of embedding model: dense retrieval, multi-vector retrieval, and sparse retrieval.'
locked:
- context_window_tokens
- fingerprint.context_window_tokens
tags:
- sentence-similarity
- xlm-roberta
- dense
- mha
updated: '2026-11-22'
sources:
- url: https://huggingface.co/BAAI/bge-m3
  title: BAAI/bge-m3 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/BAAI/bge-m3/blob/main/config.json
  title: BAAI/bge-m3 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2402.03216
  title: 'M3-Embedding: Multi-Linguality, Multi-Functionality, Multi-Granularity Text Embeddings Through Self-Knowledge Distillation'
  trust: 0.95
- url: https://arxiv.org/abs/2004.04906
  title: Dense Passage Retrieval for Open-Domain Question Answering
  trust: 0.95
- url: https://arxiv.org/abs/2106.14807
  title: A Few Brief Notes on DeepImpact, COIL, and a Conceptual Framework for Information Retrieval Techniques
  trust: 0.95
- url: https://arxiv.org/abs/2107.05720
  title: 'SPLADE: Sparse Lexical and Expansion Model for First Stage Ranking'
  trust: 0.95
- url: https://arxiv.org/abs/2004.12832
  title: 'ColBERT: Efficient and Effective Passage Search via Contextualized Late Interaction over BERT'
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/xlm_roberta/modeling_xlm_roberta.py
  title: 'transformers/models/xlm_roberta/modeling_xlm_roberta.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
- url: https://huggingface.co/BAAI/bge-m3/blob/main/pytorch_model.bin
  title: 'BAAI/bge-m3 — pytorch_model.bin: tensor shapes of the published checkpoint, summed by code for the parameter count'
  trust: 0.9
- url: https://huggingface.co/BAAI/bge-m3/blob/main/1_Pooling/config.json
  title: BAAI/bge-m3 — 1_Pooling/config.json
  trust: 0.9
---

## Summary

BGE-M3 is a text embedding model from the Beijing Academy of Artificial Intelligence (BAAI). It turns a sentence or a whole document into a list of numbers, called a vector, so that a search system can find texts with a similar meaning by comparing their vectors. Its model card states support for more than 100 languages and inputs of up to 8192 tokens.

## Intended Use

The model card presents it for the retrieval step of retrieval-augmented generation (RAG), where a system looks up relevant text before a language model writes an answer. For that step the card recommends hybrid retrieval, which combines meaning-based and keyword-based results, followed by a re-ranking model.

## What Sets It Apart

Its makers describe one model that returns three kinds of output at once. A dense output is a single vector per text, a sparse output is a weight for each token present in the text (the keyword-style matching that BM25 does), and a multi-vector output represents the text with several vectors, as ColBERT does. The card also states that it works across more than 100 languages and on inputs from a short sentence to a document of 8192 tokens.
