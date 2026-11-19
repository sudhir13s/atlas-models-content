---
id: concept/silu-swish
type: concept
slug: silu-swish
schema_version: 1.2
title: SiLU / Swish
category: activation
code: silu
aliases:
- swish
order: 1
short_description: Smooth, non-monotonic activation — the modern default.
example_models:
- Llama
- Mistral
- Qwen
- DeepSeek
tags:
- activation
- silu
updated: '2026-11-19'
sources: []
---

## What It Is

SiLU (Sigmoid Linear Unit), also called Swish, is x * sigmoid(x). Unlike ReLU, it is smooth everywhere and slightly non-monotonic (dips below zero near x=-1). This allows the network to learn slightly different patterns than ReLU.

## Why It Matters

Empirically outperforms ReLU and GELU on most language modeling benchmarks. The smoothness helps gradient flow during training. Most modern LLMs use SiLU or its gated variant (SwiGLU).

## Trade-off

~10% more compute than ReLU (requires sigmoid computation). In practice this is negligible compared to attention costs.
