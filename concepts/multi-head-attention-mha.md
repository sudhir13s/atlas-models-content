---
id: concept/multi-head-attention-mha
type: concept
slug: multi-head-attention-mha
schema_version: 1.2
title: Multi-Head Attention (MHA)
category: attention
code: MHA
aliases: []
order: 1
short_description: Every head has its own Key, Query, and Value projections.
example_models:
- GPT-2
- GPT-3
- BERT
- Pythia
- OPT
tags:
- attention
- mha
updated: '2026-11-19'
sources: []
---

## What It Is

The original attention mechanism from 'Attention Is All You Need.' Each attention head independently learns what to pay attention to by maintaining separate Key, Query, and Value weight matrices. With 32 heads, you have 32 independent K, Q, and V projections.

## Why It Matters

Maximum expressiveness — each head can specialize in different patterns (syntax, semantics, positional). This is the gold standard for quality but uses the most memory during inference because every head's KV pairs must be cached.

## Trade-off

Best quality, highest KV cache memory cost. For a 32-head model, you store 32 separate KV pairs per token. This makes long-context inference expensive.
