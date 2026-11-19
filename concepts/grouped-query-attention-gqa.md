---
id: concept/grouped-query-attention-gqa
type: concept
slug: grouped-query-attention-gqa
schema_version: 1.2
title: Grouped Query Attention (GQA)
category: attention
code: GQA
aliases: []
order: 2
short_description: Multiple query heads share fewer KV heads — balances quality and speed.
example_models:
- Llama 2/3
- Mistral
- Qwen 2.5
- Gemma 2
- DeepSeek-V2
- Command R
tags:
- attention
- gqa
updated: '2026-11-19'
sources: []
---

## What It Is

A middle ground introduced in the Llama 2 paper. Query heads are grouped, and each group shares one set of Key-Value heads. For example, 32 query heads with 8 KV heads means 4 query heads share each KV head. This reduces KV cache size by 4x while retaining most of MHA's quality.

## Why It Matters

The dominant choice for modern LLMs because it dramatically reduces memory during inference (smaller KV cache) with minimal quality loss. This means longer contexts and higher throughput on the same hardware.

## Trade-off

Slight quality reduction vs MHA (usually <1% on benchmarks), but 2-8x less KV cache memory. The sweet spot for production deployment.
