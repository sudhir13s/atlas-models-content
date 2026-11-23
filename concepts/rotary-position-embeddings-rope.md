---
id: concept/rotary-position-embeddings-rope
type: concept
slug: rotary-position-embeddings-rope
schema_version: 3.1
title: Rotary Position Embeddings (RoPE)
category: positional-encoding
code: RoPE
aliases: []
order: 1
short_description: Encodes position by rotating the query and key vectors, so attention depends on relative distance.
example_models:
- Llama
- Mistral
- Qwen
- DeepSeek
- Gemma
- Yi
tags:
- positional-encoding
- rope
updated: '2026-11-21'
sources:
- url: https://arxiv.org/abs/2104.09864
  title: 'RoFormer: Enhanced Transformer with Rotary Position Embedding (arXiv:2104.09864)'
  trust: 0.95
- url: https://arxiv.org/abs/2302.13971
  title: 'LLaMA: Open and Efficient Foundation Language Models (arXiv:2302.13971)'
  trust: 0.95
- url: https://arxiv.org/abs/2306.15595
  title: Extending Context Window of Large Language Models via Positional Interpolation (arXiv:2306.15595)
  trust: 0.95
- url: https://arxiv.org/abs/2309.00071
  title: 'YaRN: Efficient Context Window Extension of Large Language Models (arXiv:2309.00071)'
  trust: 0.95
---

## What It Is

RoPE encodes each token's absolute position with a rotation matrix applied to the query and key vectors. The attention score between two tokens then depends explicitly on their relative position. Introduced by Jianlin Su and co-authors in 2021 (RoFormer); LLaMA adds it at each layer in place of absolute position embeddings.

## Why It Matters

The authors list three properties: flexibility of sequence length, inter-token dependency that decays with relative distance, and compatibility with linear self-attention.

## Trade-off

The YaRN authors state that RoPE models fail to generalize past the sequence length they were trained on. The Position Interpolation authors report that extrapolating beyond it can produce catastrophically high attention scores, so extension methods rescale positions and fine-tune.
