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
short_description: Simpler, faster normalization — skips the mean subtraction step.
example_models:
- Llama
- Mistral
- Qwen
- Gemma
- DeepSeek
tags:
- normalization
- rmsnorm
updated: '2026-11-19'
sources: []
---

## What It Is

Normalizes activations by dividing by the root mean square of the values, without first subtracting the mean (which LayerNorm does). This saves one reduction operation per normalization. Introduced by Zhang and Sennrich (2019).

## Why It Matters

~10-15% faster than LayerNorm in practice because it skips the mean computation. With thousands of normalization operations per forward pass, this adds up. Quality is equivalent or better on modern LLM benchmarks.

## Trade-off

Effectively no downside — RMSNorm is a strict improvement for transformer language models. LayerNorm's mean-centering step turns out to be unnecessary for the LM task.
