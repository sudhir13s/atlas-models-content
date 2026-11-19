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
short_description: Probabilistic activation — the BERT-era default.
example_models:
- BERT
- GPT-2
- Pythia
- OPT
- BLOOM
tags:
- activation
- gelu
updated: '2026-11-19'
sources: []
---

## What It Is

GELU multiplies the input by the probability that a standard Gaussian would be less than the input: x * Phi(x). It is smooth and handles negative values more gracefully than ReLU.

## Why It Matters

Was the dominant activation for BERT-era models. Still used in some architectures. Slightly less popular than SiLU in modern decoder-only LLMs.

## Trade-off

Similar compute cost to SiLU. Both outperform ReLU. The choice between GELU and SiLU is often empirical and model-specific.
