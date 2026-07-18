---
id: parameter/num-attention-heads
type: parameter
slug: num-attention-heads
schema_version: 1.0
title: "Number of Attention Heads"
name: num_attention_heads
priority: 5
importance: critical
applies_to: [llm, transformer, moe]
range: "12 → 128 heads"
controls: "How many parallel attention sub-spaces each layer computes. Together with head_dim it partitions the hidden size across independent attention patterns."
formula: "num_heads × head_dim = hidden_size; Q and O projections are ≈ hidden × (num_heads · head_dim) = d² each"
if_increased: "More distinct attention patterns per layer (finer relational resolution), but if hidden_size is fixed each head gets thinner (smaller head_dim)."
if_decreased: "Fewer, wider heads — each attends over a larger sub-space, but the layer captures fewer independent relations."
tags: [llm, transformer, attention, heads, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
---

## Definition
`num_attention_heads` is how many independent attention "heads" run in parallel within each layer.
Multi-head attention splits the hidden dimension into `num_heads` sub-spaces of size `head_dim`, lets
each head learn a different relational pattern (syntax, coreference, position), then concatenates and
projects the results.

## What to look for
`num_heads` and `head_dim` are coupled by `hidden_size = num_heads × head_dim`. Because `head_dim` is
usually held near 64–128 for good compute efficiency, `num_heads` scales with width:

- 12 heads at `d = 768` (GPT-2 small), 32 heads at `d = 4096` (Llama 3 8B), 64 heads at `d = 8192`
  (Llama 3 70B).

`num_heads` must divide `hidden_size` evenly, and with grouped-query attention it must be a multiple
of `num_key_value_heads`.

## Calculation
The head count itself does not change the total attention parameter count when `head_dim` is set by
`hidden_size / num_heads` — the Q and O projections stay ≈ `d²` each regardless of how the `d`
dimension is sliced. It changes *how* attention is computed, and it interacts with GQA (which reduces
the K/V projections) and with the KV-cache size at inference.

$$Q,\,O \approx d^2 \text{ each};\qquad \text{head\_dim} = \frac{\text{hidden\_size}}{\text{num\_heads}}$$

## Example
Llama 3 8B: 32 heads, `head_dim = 4096 / 32 = 128`. Q projection = `4096 × (32 × 128) = 4096² ≈
16.8M`; likewise O ≈ 16.8M. Doubling to 64 heads (with `head_dim = 64`) leaves those projection
counts unchanged but gives the layer twice as many, thinner attention patterns.
