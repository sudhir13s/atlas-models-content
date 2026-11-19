---
id: concept/attention-with-linear-biases-alibi
type: concept
slug: attention-with-linear-biases-alibi
schema_version: 1.2
title: Attention with Linear Biases (ALiBi)
category: positional-encoding
code: ALiBi
aliases: []
order: 2
short_description: Adds a simple linear penalty based on distance — no learned parameters.
example_models:
- BLOOM
- MPT
- Falcon (some variants)
tags:
- positional-encoding
- alibi
updated: '2026-11-19'
sources: []
---

## What It Is

Instead of adding position embeddings to tokens, ALiBi subtracts a linear bias from attention scores based on the distance between the query and key tokens. Closer tokens get higher attention. Each head uses a different slope, giving different 'attention spans.' Introduced by Press et al. (2022).

## Why It Matters

Zero extra parameters, zero extra computation during the forward pass. The linear bias naturally allows extrapolation to longer sequences than seen during training without any special scaling technique.

## Trade-off

The linear decay assumption is simplistic — some tasks benefit from attending strongly to distant tokens (e.g., long-range dependencies in code). RoPE models with scaling tend to handle very long contexts more gracefully.
