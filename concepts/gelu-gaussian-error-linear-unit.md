---
id: concept/gelu-gaussian-error-linear-unit
type: concept
slug: gelu-gaussian-error-linear-unit
schema_version: 1.2
title: GELU (Gaussian Error Linear Unit)
category: activation
code: gelu
aliases:
- gelu_new
order: 2
short_description: Weights each input by the standard Gaussian CDF of its value, x·Φ(x).
example_models:
- BERT
- GPT-2
- Pythia
tags:
- activation
- gelu
updated: '2026-11-21'
sources:
- url: https://arxiv.org/abs/1606.08415
  title: Gaussian Error Linear Units (GELUs) (arXiv:1606.08415)
  trust: 0.95
- url: https://arxiv.org/abs/1810.04805
  title: 'BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (arXiv:1810.04805)'
  trust: 0.95
- url: https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf
  title: Improving Language Understanding by Generative Pre-Training (Radford et al., 2018)
  trust: 0.9
---

## What It Is

GELU is x·Φ(x), where Φ is the standard Gaussian cumulative distribution function. Introduced by Dan Hendrycks and Kevin Gimpel in 2016. The authors contrast it with ReLU: GELU weights inputs by their value, while ReLU gates inputs by their sign.

## Why It Matters

The authors report improvements over ReLU and ELU on all the computer vision, language and speech tasks they tested. GPT used GELU, and BERT adopted it in place of ReLU, following GPT.

## Trade-off

Φ has no closed form, so the authors give two approximations: 0.5x(1 + tanh[√(2/π)(x + 0.044715x³)]) and x·σ(1.702x).
