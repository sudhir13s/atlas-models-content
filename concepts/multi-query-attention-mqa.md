---
id: concept/multi-query-attention-mqa
type: concept
slug: multi-query-attention-mqa
schema_version: 3.1
title: Multi-Query Attention (MQA)
category: attention
code: MQA
aliases: []
order: 3
short_description: All query heads share a single key-value head.
example_models:
- PaLM
- Falcon-7B
- StarCoder
tags:
- attention
- mqa
updated: '2026-11-21'
sources:
- url: https://arxiv.org/abs/1911.02150
  title: 'Fast Transformer Decoding: One Write-Head is All You Need (arXiv:1911.02150)'
  trust: 0.95
- url: https://arxiv.org/abs/2204.02311
  title: 'PaLM: Scaling Language Modeling with Pathways (arXiv:2204.02311)'
  trust: 0.95
- url: https://arxiv.org/abs/2305.06161
  title: 'StarCoder: may the source be with you! (arXiv:2305.06161)'
  trust: 0.95
- url: https://arxiv.org/abs/2305.13245
  title: 'GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints (arXiv:2305.13245)'
  trust: 0.95
---

## What It Is

All query heads share one key head and one value head; only the query projections differ across heads. Introduced by Noam Shazeer in 2019. PaLM uses it: keys and values are projected to a single head while queries keep one projection per head.

## Why It Matters

Sharing keys and values shrinks the tensors that must be loaded at every decoding step. Shazeer reports that the resulting models decode much faster with only minor quality degradation; the PaLM authors report significant cost savings at autoregressive decoding time.

## Trade-off

The GQA authors note that MQA can lead to quality degradation, and that it may not be desirable to train a separate model only for faster inference. Grouped-query attention was proposed as the intermediate setting.
