---
id: concept/multi-head-attention-mha
type: concept
slug: multi-head-attention-mha
schema_version: 3.1
title: Multi-Head Attention (MHA)
category: attention
code: MHA
aliases: []
order: 1
short_description: Every head has its own Key, Query, and Value projections.
example_models:
- GPT-2
- BERT
- Pythia
- OPT
tags:
- attention
- mha
updated: '2026-11-21'
sources:
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (arXiv:1706.03762)
  trust: 0.95
- url: https://arxiv.org/abs/1911.02150
  title: 'Fast Transformer Decoding: One Write-Head is All You Need (arXiv:1911.02150)'
  trust: 0.95
---

## What It Is

The attention mechanism of the original Transformer paper, 'Attention Is All You Need' (Vaswani et al., 2017). Each head projects the input with its own query, key and value matrices, attends, and the head outputs are concatenated and projected again. The base model in that paper uses 8 heads of dimension 64.

## Why It Matters

The authors write that multiple heads let the model jointly attend to information from different representation subspaces at different positions. Because each head has a smaller dimension, the total compute is similar to single-head attention at full dimension.

## Trade-off

Every head keeps its own keys and values, so all of them must be cached during generation. Shazeer (2019) identifies the memory bandwidth of repeatedly loading these key and value tensors as the reason incremental decoding is often slow.
