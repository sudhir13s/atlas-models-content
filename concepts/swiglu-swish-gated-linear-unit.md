---
id: concept/swiglu-swish-gated-linear-unit
type: concept
slug: swiglu-swish-gated-linear-unit
schema_version: 1.2
title: SwiGLU (Swish-Gated Linear Unit)
category: activation
code: swiglu
aliases: []
order: 3
short_description: Gated SiLU — splits the FFN into two paths and multiplies them.
example_models:
- Llama 2/3
- Mistral
- Gemma 2
- PaLM
- Command R
tags:
- activation
- swiglu
updated: '2026-11-19'
sources: []
---

## What It Is

Combines SiLU with a gating mechanism: the feed-forward layer is split into two parallel projections. One goes through SiLU, the other stays linear, and they are multiplied together. This 'gated' approach gives the network more control over information flow.

## Why It Matters

The most popular FFN activation in modern LLMs. The gating adds expressive power with only marginal extra compute. Introduced by Shazeer (2020) and widely adopted after PaLM and Llama showed strong results.

## Trade-off

Requires ~33% more FFN parameters (three projections instead of two). Most architectures reduce the hidden dimension to compensate, keeping total params similar.
