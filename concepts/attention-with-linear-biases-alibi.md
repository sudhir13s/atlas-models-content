---
id: concept/attention-with-linear-biases-alibi
type: concept
slug: attention-with-linear-biases-alibi
schema_version: 3.1
title: Attention with Linear Biases (ALiBi)
category: positional-encoding
code: ALiBi
aliases: []
order: 2
short_description: Adds a fixed penalty proportional to query-key distance to attention scores; no learned parameters.
example_models:
- BLOOM
tags:
- positional-encoding
- alibi
updated: '2026-11-21'
sources:
- url: https://arxiv.org/abs/2108.12409
  title: 'Train Short, Test Long: Attention with Linear Biases Enables Input Length Extrapolation (arXiv:2108.12409)'
  trust: 0.95
---

## What It Is

ALiBi adds no position embeddings anywhere in the network. Instead, after the query-key dot product it adds a static bias proportional to the distance between the two tokens. Each head has its own slope, fixed before training: for 8 heads the slopes form a geometric sequence. Introduced by Ofir Press, Noah A. Smith and Mike Lewis (arXiv 2021, ICLR 2022).

## Why It Matters

The authors report that a 1.3 billion parameter model trained on 1,024-token inputs extrapolates to 2,048 tokens. It reaches the perplexity of a sinusoidal model trained on 2,048 tokens while training 11% faster and using 11% less memory.

## Trade-off

The authors describe an inductive bias towards recency. Their own analysis suggests that on inputs longer than the training length, ALiBi may not use the longer context: the gain may come from fewer tokens lacking context near the start of each window.
