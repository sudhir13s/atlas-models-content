---
id: model/embeddinggemma-2
type: model
slug: embeddinggemma-2
schema_version: 1.1
title: embeddinggemma-2
organization: organization/google-deepmind
release_date: '2026-09-14'
release_year: 2026
release_month: 9
license: license/apache-2-0
model_category: retrieval
tasks:
- task/embedding-models
parameters_active_b: 0.74
total_parameters: 744371512
context_window_tokens: 8192
embedding_dimension: 768
summary: EmbeddingGemma 2 is an embedding model from Google DeepMind. An embedding model turns an input into a list of numbers, called a vector, so that similar inputs end up close together. This one maps text (including code), images, video and audio into a single 768-dimensional vector space. The card gives 740M total parameters, support for 100+ languages and an 8K-token context window.
intended_use: The card says it is designed to run on consumer hardware such as mobile devices and laptops, for on-device applications like search, retrieval-augmented generation (RAG), classification and clustering.
what_sets_it_apart: The card lists these design points. It builds on the architecture of Gemma 4 and puts four input types in one shared vector space. A 270M-parameter text model is paired with vision (170M) and audio (300M) encoders that can be loaded separately, so only the needed input types are loaded. Matryoshka Representation Learning lets the vector be cut to 512, 256 or 128 numbers, which the card says cuts storage by up to 6 times. Short instruction prefixes in the text tune the embedding for a task.
running_it: The card says text inputs should start with a short task prefix, such as one for search queries and a title-and-text format for documents. Omitting the prefix still works but reduces precision. Prefixes apply to text only; images, video and audio are passed without one. Truncated vectors of 128, 256 or 512 numbers should be re-normalized, and the card says 128 is best suited to text-only workloads.
fingerprint:
  attention_type: GQA
  num_layers: 24
  attention_heads: 4
  kv_heads: 2
  head_size: 256
  hidden_size: 512
  intermediate_size: 2048
  context_window_tokens: 8192
  vocabulary_size: 262144
  normalization: RMSNorm
  norm_placement: both
  stack: encoder
  positional_encoding: RoPE
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/embedding_gemma2/modeling_embedding_gemma2.py
  summary: Text goes in as tokens. An image or video goes through the vision tower and audio through the audio tower; each tower's vectors are projected and take their places among the token embeddings. One encoder reads the joined sequence, and a last projection gives the vectors that are pooled into the embedding. The second figure shows one encoder block.
  side_into: merge
  stages:
  - id: image
    role: input
    title: Image or video
    side: true
    into: merge
  - id: vision-tower
    role: visionEncoder
    title: Vision tower
    details:
    - 16 blocks · width 768
    side: true
  - id: vision-embedder
    role: projector
    title: RMSNorm + projection
    details:
    - to width 512
    note: A norm and one linear layer map the vision tower's vectors to the width of the token embeddings.
    side: true
  - id: audio
    role: input
    title: Audio
    side: true
    into: merge
  - id: audio-tower
    role: audioEncoder
    title: Audio tower
    details:
    - 12 blocks
    side: true
  - id: audio-embedder
    role: projector
    title: RMSNorm + projection
    details:
    - to width 512
    note: A norm and one linear layer map the audio tower's vectors to the width of the token embeddings.
    side: true
  - id: tokens
    role: tokenizer
    title: Text tokens
    details:
    - vocabulary 262,144
  - id: embedding
    role: embedding
    title: Token embedding
    details:
    - width 512
  - id: merge
    role: embedding
    title: Place among text embeddings
    details:
    - at the image and audio positions
    note: The projected image and audio vectors take the places the prompt reserves for them, so every input forms one sequence.
  - id: block
    role: attention
    title: Attention + feed-forward
    details:
    - 4 heads · 2 KV heads
    frame: Encoder block
    repeat: 24
  - id: norm
    role: normalisation
    title: Final RMSNorm
  - id: projection
    role: projector
    title: Linear projection
    details:
    - width 512 → 768
  - id: output
    role: outputHead
    title: Embedding output
    details:
    - one vector per token, then pooled
    note: The model returns one 768-number vector per token. It is meant to be wrapped by mean pooling and normalization, which turn them into one embedding.
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: feature-extraction
  library: transformers
hub_signals:
  downloads_30d: 612777
  likes: 3561
  last_modified: '2026-10-28'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/embeddinggemma-2
aliases:
- google/embeddinggemma-2
lineage:
  parents: []
  children: []
quotes:
  summary: is an open multimodal embedding model built by Google DeepMind
  intended_use: Designed to run on consumer hardware such as mobile devices and laptops
  what_sets_it_apart: allowing developers to load only the modalities required for their use case
  running_it: Using the right prefix improves quality; omitting it still works but reduces precision.
locked:
- context_window_tokens
- fingerprint.context_window_tokens
tags:
- feature-extraction
- embedding-gemma2
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/embeddinggemma-2
  title: google/embeddinggemma-2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/embeddinggemma-2/blob/main/config.json
  title: google/embeddinggemma-2 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2610.37364
  title: arXiv:2610.37364
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/embedding_gemma2/modeling_embedding_gemma2.py
  title: 'transformers/models/embedding_gemma2/modeling_embedding_gemma2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

EmbeddingGemma 2 is an embedding model from Google DeepMind. An embedding model turns an input into a list of numbers, called a vector, so that similar inputs end up close together. This one maps text (including code), images, video and audio into a single 768-dimensional vector space. The card gives 740M total parameters, support for 100+ languages and an 8K-token context window.

## Intended Use

The card says it is designed to run on consumer hardware such as mobile devices and laptops, for on-device applications like search, retrieval-augmented generation (RAG), classification and clustering.

## What Sets It Apart

The card lists these design points. It builds on the architecture of Gemma 4 and puts four input types in one shared vector space. A 270M-parameter text model is paired with vision (170M) and audio (300M) encoders that can be loaded separately, so only the needed input types are loaded. Matryoshka Representation Learning lets the vector be cut to 512, 256 or 128 numbers, which the card says cuts storage by up to 6 times. Short instruction prefixes in the text tune the embedding for a task.

## Running It

The card says text inputs should start with a short task prefix, such as one for search queries and a title-and-text format for documents. Omitting the prefix still works but reduces precision. Prefixes apply to text only; images, video and audio are passed without one. Truncated vectors of 128, 256 or 512 numbers should be re-normalized, and the card says 128 is best suited to text-only workloads.
