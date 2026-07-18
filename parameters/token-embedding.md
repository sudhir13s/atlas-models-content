---
id: parameter/token-embedding
type: parameter
slug: token-embedding
schema_version: 1.0
title: "Token Embedding Table"
name: embed_tokens
priority: 6
importance: critical
applies_to: [llm, transformer, moe]
range: "vocab_size × hidden_size (e.g. 525M for Llama 3 8B)"
controls: "The lookup table that maps each token ID to its initial hidden vector — the model's input representation."
formula: "embedding params = vocab_size × hidden_size"
if_increased: "A larger table (bigger vocab or wider model) gives richer input representations but adds parameters that do no per-token compute in the body."
if_decreased: "Fewer embedding parameters, but a smaller vocab lengthens sequences or a narrower model bottlenecks the residual stream."
tags: [llm, transformer, embedding, input, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1608.05859", title: "Using the Output Embedding to Improve Language Models (Press & Wolf, 2017)", trust: 0.85 }
---

## Definition
The **token embedding table** (`embed_tokens`, the "wte" in GPT-style code) is a `vocab_size ×
hidden_size` matrix. Each row is the learned vector for one token ID; the first thing the model does
is look up the row for every input token to build the initial residual stream.

## What to look for
The embedding table stores knowledge but performs no per-token matrix multiply in the transformer
body — it is a lookup. Its size is driven entirely by `vocab_size` and `hidden_size`:

- In **large** models it is a small slice of the total (≈13% for Llama 3 8B).
- In **small** models it can dominate, which is why weight tying (sharing it with the output head) is
  standard below a few hundred million parameters.

## Calculation
$$\text{embedding params} = V \times d$$

with `V` = `vocab_size`, `d` = `hidden_size`. When embeddings are **tied**, this same matrix is
reused (transposed) as the output head, so it is counted once; when **untied**, the output head adds
another `V × d`.

## Example
Llama 3 8B: `128256 × 4096 ≈ 525.3M` parameters in the embedding table. A 160M-parameter small model
with the same 128k vocab and `d = 768` would spend `128256 × 768 ≈ 98.5M` on the table — over half
the model — making tying essential there.
