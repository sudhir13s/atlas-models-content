---
id: concept/learned-position-embeddings
type: concept
slug: learned-position-embeddings
schema_version: 3.1
title: Learned Position Embeddings
category: positional-encoding
code: Learned
aliases: []
order: 3
short_description: A trainable vector for each position, added to the token embedding.
example_models:
- GPT-2
- BERT
- OPT
tags:
- positional-encoding
- learned
updated: '2026-11-21'
sources:
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (arXiv:1706.03762)
  trust: 0.95
- url: https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf
  title: Improving Language Understanding by Generative Pre-Training (Radford et al., 2018)
  trust: 0.9
- url: https://arxiv.org/abs/1810.04805
  title: 'BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (arXiv:1810.04805)'
  trust: 0.95
- url: https://arxiv.org/abs/2108.12409
  title: 'Train Short, Test Long: Attention with Linear Biases Enables Input Length Extrapolation (arXiv:2108.12409)'
  trust: 0.95
---

## What It Is

Each position up to a maximum length gets its own embedding vector, learned during training and added to the token embedding. GPT used learned position embeddings instead of the sinusoidal encoding of the original Transformer, and BERT learns its position embeddings too.

## Why It Matters

The original Transformer authors tried learned position embeddings and found results nearly identical to their sinusoidal encoding. They chose the sinusoidal version because it may extrapolate to longer sequences.

## Trade-off

The ALiBi authors note that learned position embeddings have no way to encode positions beyond the training length, so they cannot extrapolate.
