---
id: concept/rotary-position-embeddings-rope
type: concept
slug: rotary-position-embeddings-rope
schema_version: 1.2
title: Rotary Position Embeddings (RoPE)
category: positional-encoding
code: RoPE
aliases: []
order: 1
short_description: Encodes position by rotating the query/key vectors — enables context extension.
example_models:
- Llama
- Mistral
- Qwen
- DeepSeek
- Gemma
- Command R
- Yi
tags:
- positional-encoding
- rope
updated: '2026-11-19'
sources: []
---

## What It Is

Injects position information by rotating query and key vectors in 2D subspaces. The rotation angle depends on the token's position, so the dot product between any two tokens naturally encodes their relative distance. Introduced by Su et al. (2021).

## Why It Matters

RoPE is the dominant positional encoding because it naturally captures relative position (not absolute), decays attention over distance, and critically, can be extended beyond the training context length using techniques like NTK-aware scaling, YaRN, or simply training with longer rope_theta values.

## Trade-off

Requires careful tuning of the base frequency (rope_theta). Higher values (500k-10M) enable longer contexts but may reduce precision at short distances. The extension techniques (YaRN, NTK) are not free — some quality loss at extreme lengths.
