---
id: parameter/tied-embeddings
type: parameter
slug: tied-embeddings
schema_version: 1.0
title: "Tied vs Untied Embeddings"
name: tie_word_embeddings
priority: 14
importance: optional
applies_to: [llm, transformer, moe]
range: "tied (share) · untied (separate)"
controls: "Whether the input embedding table and the output head share the same weight matrix. Tying saves one vocab_size × hidden_size matrix."
formula: "tied → output head costs 0; untied → output head costs vocab_size × hidden_size"
if_increased: "(Untie) the model gets an independent output projection — more freedom, but a full V×d matrix of extra parameters."
if_decreased: "(Tie) reuse the embedding as the head, removing V×d parameters — a big saving for small-vocab-heavy models."
tags: [llm, transformer, weight-tying, embedding, output, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1608.05859", title: "Using the Output Embedding to Improve Language Models (Press & Wolf, 2017)", trust: 0.85 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
---

## Definition
**Weight tying** (`tie_word_embeddings`) decides whether the input **token embedding** and the output
**unembedding head** are the *same* matrix (shared, transposed) or two independent matrices. Both are
`vocab_size × hidden_size`, so the decision is worth exactly one such matrix.

## What to look for
The trade-off scales with model size:

- **Small models** almost always **tie** — the embedding is a large fraction of the budget, and
  spending it twice is wasteful. Tying also acts as a mild regularizer.
- **Large models** often **untie** (Llama 3, GPT-3) — the extra matrix is a small fraction of the
  total and giving the output its own representation buys a little quality.

## Calculation
$$\text{output head} = \begin{cases} 0 & \text{tied} \\ V \times d & \text{untied} \end{cases}$$

The embedding table itself is always `V × d`; tying only removes the *duplicate* on the output side.

## Example
Llama 3 8B is **untied**: embedding `525.3M` + a separate head `525.3M` = `1.05B` token-related
parameters. Tying them would collapse that to a single `525.3M`, saving ≈6.5% of the model — a saving
worth taking in a 160M model (where it can halve the network) but skipped at 8B scale.
