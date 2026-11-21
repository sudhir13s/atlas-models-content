---
id: concept/root-mean-square-normalization-rmsnorm
type: concept
slug: root-mean-square-normalization-rmsnorm
schema_version: 1.2
title: Root Mean Square Normalization (RMSNorm)
category: normalization
code: RMSNorm
aliases: []
order: 1
short_description: Normalizes by the root mean square of the inputs, without subtracting the mean.
example_models:
- Llama
- Mistral
- Qwen
- Gemma
- DeepSeek
tags:
- normalization
- rmsnorm
updated: '2026-11-21'
sources:
- url: https://arxiv.org/abs/1910.07467
  title: Root Mean Square Layer Normalization (arXiv:1910.07467)
  trust: 0.95
- url: https://arxiv.org/abs/2302.13971
  title: 'LLaMA: Open and Efficient Foundation Language Models (arXiv:2302.13971)'
  trust: 0.95
---

## What It Is

Normalizes the summed inputs to a layer's neurons by their root mean square, without first subtracting the mean as LayerNorm does. Introduced by Biao Zhang and Rico Sennrich (NeurIPS 2019). LLaMA uses it to normalize the input of each Transformer sub-layer.

## Why It Matters

The authors describe RMSNorm as computationally simpler than LayerNorm. They report comparable performance with running time reduced by 7% to 64% across different models.

## Trade-off

RMSNorm keeps re-scaling invariance but gives up LayerNorm's re-centering invariance. The authors hypothesize that re-centering is dispensable.
