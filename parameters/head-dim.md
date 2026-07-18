---
id: parameter/head-dim
type: parameter
slug: head-dim
schema_version: 1.0
title: "Head Dimension"
name: head_dim
priority: 7
importance: important
applies_to: [llm, transformer, moe]
range: "64 → 128 (typical)"
controls: "The width of a single attention head — the size of the sub-space each head projects queries, keys, and values into."
formula: "head_dim = hidden_size / num_attention_heads (when not set independently)"
if_increased: "Each head reasons over a richer sub-space, but with hidden_size fixed you get fewer heads; a larger head_dim also enlarges the per-token KV cache."
if_decreased: "More heads fit in the same width, but each head is thinner and may under-resolve fine relations."
tags: [llm, transformer, attention, head-dim, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2305.13245", title: "GQA: Training Generalized Multi-Query Transformer Models (Ainslie et al., 2023)", trust: 0.9 }
---

## Definition
`head_dim` is the dimensionality of each individual attention head. Multi-head attention slices the
`hidden_size` residual stream into `num_attention_heads` pieces of size `head_dim`, computes attention
inside each, then concatenates.

In most transformers `head_dim` is not a free parameter — it is `hidden_size / num_attention_heads` —
but some models (e.g. certain Llama and Gemma variants) set it independently.

## What to look for
`head_dim` is usually kept in the **64–128** range: large enough for the dot-product attention to be
expressive, small enough to stay efficient on tensor cores. It also sets the **RoPE** rotation
dimension and the **KV-cache** footprint per token (`2 × num_kv_heads × head_dim` per layer).

Values far below 64 tend to under-perform; values far above 128 add KV-cache memory with diminishing
returns.

## Calculation
When derived: `head_dim = hidden_size / num_attention_heads`. The Q/K/V/O projection sizes depend on
`num_heads × head_dim` (which equals `hidden_size` in the standard case), so changing `head_dim`
while holding that product fixed does not change parameter count — it re-partitions the same weights.

$$\text{head\_dim} = \frac{\text{hidden\_size}}{\text{num\_attention\_heads}}$$

## Example
Llama 3 8B: `hidden_size = 4096`, `num_attention_heads = 32` → `head_dim = 128`. Its 8 KV heads
(grouped-query attention) each also use `head_dim = 128`, so the per-layer KV cache is
`2 × 8 × 128 = 2048` values per token — one quarter of a full multi-head cache.
