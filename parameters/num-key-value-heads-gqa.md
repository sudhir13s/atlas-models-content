---
id: parameter/num-key-value-heads-gqa
type: parameter
slug: num-key-value-heads-gqa
schema_version: 1.0
title: "Number of Key/Value Heads (GQA / MQA)"
name: num_key_value_heads
priority: 8
importance: important
applies_to: [llm, transformer, moe]
range: "1 (MQA) → num_attention_heads (MHA)"
controls: "How many distinct key/value projections the attention shares across query heads. It sets the KV-cache size and shrinks the K/V weight matrices."
formula: "K, V projections = 2 × hidden × (num_kv_heads × head_dim); KV cache ∝ num_kv_heads"
if_increased: "Closer to full multi-head attention — more expressive K/V, but a larger KV cache and more inference memory/bandwidth."
if_decreased: "Toward multi-query attention — much smaller KV cache and faster decoding, with a small quality cost that GQA mostly recovers."
tags: [llm, transformer, attention, gqa, mqa, kv-cache, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2305.13245", title: "GQA: Training Generalized Multi-Query Transformer Models (Ainslie et al., 2023)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1911.02150", title: "Fast Transformer Decoding: One Write-Head is All You Need (MQA, Shazeer 2019)", trust: 0.85 }
---

## Definition
`num_key_value_heads` is how many **key/value** heads the attention uses, which query heads then
share. It is the knob behind **grouped-query attention (GQA)**:

- `num_kv_heads = num_attention_heads` → full **multi-head attention (MHA)**.
- `num_kv_heads = 1` → **multi-query attention (MQA)** — all query heads share one K/V.
- in between → **GQA**, the modern default.

## What to look for
The dominant reason GQA exists is the **KV cache**: during autoregressive decoding the model stores
K and V for every past token, and that memory scales with `num_kv_heads`, not `num_attention_heads`.
Cutting KV heads from 32 → 8 shrinks the cache 4× with little quality loss — critical for long
contexts and high-throughput serving.

Constraint: `num_attention_heads` must be a multiple of `num_key_value_heads`.

## Calculation
The Q and O projections stay at ≈ `d²` each, but K and V shrink:

$$K,V = 2 \times \text{hidden} \times (\text{num\_kv\_heads} \times \text{head\_dim})$$

so K/V weights (and the KV cache) scale by `num_kv_heads / num_attention_heads` relative to MHA.

## Example
Llama 3 8B: 32 query heads, **8** KV heads, `head_dim = 128`. K and V are each `4096 × (8 × 128) =
4.19M` (versus 16.8M under full MHA) — a 4× reduction. The KV cache drops to one quarter, which is
what lets the 8B model serve 128k-token contexts economically.
