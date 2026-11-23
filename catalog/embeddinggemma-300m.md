---
id: model/embeddinggemma-300m
type: model
slug: embeddinggemma-300m
schema_version: 3.0
title: embeddinggemma-300m
organization: organization/google-deepmind
release_date: '2025-07-17'
license: license/gemma
model_category: retrieval
model_kind: embedding
tasks:
- task/embedding-models
total_parameters: 302863104
summary: EmbeddingGemma is a 300-million-parameter text embedding model from Google, built from Gemma 3. An embedding model turns a piece of text into a list of numbers, a vector, so that texts with similar meaning can be found by comparing vectors. The card says it was trained on data in more than 100 spoken languages and accepts input of up to 2048 tokens.
intended_use: The card lists search and retrieval, classification, clustering and semantic similarity as uses. Its retrieval uses include document search, query search, code retrieval from natural-language queries, question answering and fact verification. It says the small size makes it deployable on phones, laptops and desktops.
what_sets_it_apart: The card says the 768-number output vector can be shortened to 512, 256 or 128 numbers through Matryoshka Representation Learning, a training method that keeps a short prefix of the vector useful, after which the vector is re-normalized. The card also says the model was initialized with T5Gemma and trained on about 320 billion tokens of text.
pipeline:
  source: https://arxiv.org/abs/2509.20354
  summary: Text is split into tokens and read by a 24-layer transformer in which every token sees every other token. The token vectors are averaged into one vector, and two linear layers turn it into the final embedding of 768 numbers, which can be cut to 512, 256 or 128.
  stages:
  - id: text
    role: input
    title: Text
    details:
    - up to 2048 tokens
  - id: embedding
    role: embedding
    title: Token embedding
    details:
    - width 768
  - id: block
    role: attention
    title: Attention + feed-forward
    details:
    - bidirectional attention
    note: The layers come from the encoder of an encoder-decoder version of Gemma 3. Attention is bidirectional, so each token reads the tokens before and after it.
    frame: Transformer block
    repeat: 24
  - id: pooling
    role: step
    title: Mean pooling
    details:
    - average over all tokens
    note: The token vectors are averaged along the sequence into one vector of width 768 that stands for the whole input.
  - id: up
    role: projector
    title: Linear layer
    details:
    - width 768 → 3072
    note: A linear projection raises the pooled vector to an intermediate width of 3072.
  - id: down
    role: projector
    title: Linear layer
    details:
    - width 3072 → 768
    note: A second linear projection brings it to the output width of 768.
  - id: output
    role: outputHead
    title: Embedding
    details:
    - 768 numbers · or 512, 256, 128
    note: The model was trained so the first 512, 256 or 128 numbers also work as an embedding (Matryoshka Representation Learning); the card says to re-normalize after cutting.
filters:
  size_category: tiny
  is_gated: true
  pipeline_tag: sentence-similarity
  library: sentence-transformers
hub_signals:
  downloads_30d: 972203
  likes: 2141
  last_modified: '2025-09-25'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/embeddinggemma-300m
aliases:
- google/embeddinggemma-300m
lineage:
  parents: []
  children: []
quotes:
  summary: EmbeddingGemma produces vector representations of text, making it well-suited for search and retrieval tasks, including classification, clustering, and semantic similarity search.
  intended_use: The small size and on-device focus makes it possible to deploy in environments with limited resources such as mobile phones, laptops, or desktops
  what_sets_it_apart: Output embedding dimension size of 768, with smaller options available (512, 256, or 128) via Matryoshka Representation Learning (MRL).
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/embeddinggemma-300m
  title: google/embeddinggemma-300m — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2509.20354
  title: arXiv:2509.20354
  trust: 0.95
---


