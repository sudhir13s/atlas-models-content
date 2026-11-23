---
id: concept/swiglu-swish-gated-linear-unit
type: concept
slug: swiglu-swish-gated-linear-unit
schema_version: 3.1
title: SwiGLU (Swish-Gated Linear Unit)
category: activation
code: swiglu
aliases: []
order: 3
short_description: A gated feed-forward layer, multiplying a Swish-activated projection by a linear one.
example_models:
- Llama
- Mistral
- PaLM
tags:
- activation
- swiglu
updated: '2026-11-21'
sources:
- url: https://arxiv.org/abs/2002.05202
  title: GLU Variants Improve Transformer (arXiv:2002.05202)
  trust: 0.95
- url: https://arxiv.org/abs/2204.02311
  title: 'PaLM: Scaling Language Modeling with Pathways (arXiv:2204.02311)'
  trust: 0.95
- url: https://arxiv.org/abs/2302.13971
  title: 'LLaMA: Open and Efficient Foundation Language Models (arXiv:2302.13971)'
  trust: 0.95
---

## What It Is

A variant of the gated linear unit (GLU), introduced by Noam Shazeer in 2020. The feed-forward layer computes two linear projections of the input, passes one through Swish, multiplies them element by element, and projects the result back: (Swish(xW) ⊗ xV)W₂.

## Why It Matters

Shazeer reports that GLU variants in the Transformer's feed-forward sub-layers improve quality over ReLU or GELU, with SwiGLU and GEGLU giving the best perplexities. PaLM and LLaMA both replace the ReLU feed-forward layer with SwiGLU.

## Trade-off

The layer has three weight matrices instead of two. To keep parameters and compute constant, Shazeer reduces the hidden size by a factor of 2/3; LLaMA uses a hidden size of 2/3·4d instead of 4d.
