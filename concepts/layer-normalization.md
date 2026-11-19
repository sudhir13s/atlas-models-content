---
id: concept/layer-normalization
type: concept
slug: layer-normalization
schema_version: 1.2
title: Layer Normalization
category: normalization
code: LayerNorm
aliases: []
order: 2
short_description: The classic — subtracts mean, divides by std deviation.
example_models:
- GPT-2
- BERT
- BLOOM
- OPT
- Pythia
- Falcon
tags:
- normalization
- layernorm
updated: '2026-11-19'
sources: []
---

## What It Is

Normalizes by computing the mean and variance across all features in a layer, then centering and scaling. This is the normalization from the original transformer paper (via Ba et al. 2016).

## Why It Matters

Well-understood, stable training behavior. The mean-centering helps with features that have different scales.

## Trade-off

Slightly slower than RMSNorm due to the extra mean computation. For modern LLMs, RMSNorm is preferred. LayerNorm is still standard in encoder models (BERT-family) and older architectures.
