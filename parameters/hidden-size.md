---
id: parameter/hidden-size
type: parameter
slug: hidden-size
schema_version: 1.0
title: "Hidden Size (d_model, Width)"
name: hidden_size
priority: 2
importance: critical
applies_to: [llm, transformer, moe]
range: "768 → 8192+"
controls: "The width of the residual stream — the dimension of every token vector flowing through the model. It scales attention and the FFN quadratically."
formula: "attention projections ≈ 4·d²; FFN ≈ 3·d·d_ffn (and d_ffn is usually a multiple of d)"
if_increased: "More capacity per token and richer representations, but parameter count grows roughly with d² (attention) and d (FFN width), so cost rises fast."
if_decreased: "Cheaper and lighter, but a narrow residual stream bottlenecks how much information each token can carry between layers."
tags: [llm, transformer, width, d-model, structure, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
---

## Definition
`hidden_size` (often written `d_model` or `d`) is the dimensionality of the **residual stream** —
the vector length of every token as it passes through the network. It is the model's **width**.

Because attention projects `d → d` and the feed-forward block expands from `d`, width scales almost
every weight matrix in the model, making it the second dominant lever on parameter count after depth.

## What to look for
`hidden_size` must be **divisible by `num_attention_heads`** (since `head_dim = hidden_size /
num_heads`). Typical values track model size:

- 768 (BERT-base / GPT-2 small), 2048–4096 (7B–8B class), 8192 (70B class).

A good rule of thumb from scaling studies: grow width and depth together rather than pushing either
to an extreme.

## Calculation
Width enters both major blocks:

- **Attention:** the Q, K, V, O projections are each ≈ `d × d`, so attention ≈ `4·d²` per layer
  (before GQA reduces K/V).
- **Feed-forward:** ≈ `3·d·d_ffn`, and since `d_ffn` is itself a multiple of `d`, the FFN scales
  with `d²` as well.

So per-layer parameters grow roughly with `d²`. Doubling width ≈ quadruples the per-layer cost.

## Example
Llama 3 8B uses `d = 4096` with 32 heads → `head_dim = 128`. Attention Q and O are each
`4096² ≈ 16.8M`; the SwiGLU FFN is `3 × 4096 × 14336 ≈ 176M` per layer. Halving width to 2048 would
cut each of those by ~4×, shrinking the model dramatically while also weakening its per-token capacity.
