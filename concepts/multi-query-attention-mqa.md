---
id: concept/multi-query-attention-mqa
type: concept
slug: multi-query-attention-mqa
schema_version: 1.2
title: Multi-Query Attention (MQA)
category: attention
code: MQA
aliases: []
order: 3
short_description: All query heads share a single KV head — fastest inference, lowest memory.
example_models:
- PaLM
- Falcon
- StarCoder
- MPT-7B
tags:
- attention
- mqa
updated: '2026-11-19'
sources: []
---

## What It Is

The most aggressive KV sharing: all query heads share exactly one Key head and one Value head. Only the Query projections differ across heads. This was introduced by Google in 2019 and is used in PaLM and Falcon.

## Why It Matters

Minimizes KV cache to the absolute floor — only 1 KV pair per token regardless of head count. This gives the fastest inference and lowest memory, ideal for serving at massive scale.

## Trade-off

Measurable quality loss vs MHA/GQA (1-3% on benchmarks). Some patterns that distinct KV heads would capture are lost. Most modern models prefer GQA as the better tradeoff.
