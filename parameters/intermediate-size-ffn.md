---
id: parameter/intermediate-size-ffn
type: parameter
slug: intermediate-size-ffn
schema_version: 1.0
title: "Intermediate Size (FFN Width)"
name: intermediate_size
priority: 3
importance: critical
applies_to: [llm, transformer, moe]
range: "intermediate ≈ 2.67–4 × hidden_size"
controls: "The inner width of each feed-forward block. The FFN holds the majority of a transformer's parameters (~70% in a modern LLM)."
formula: "FFN params per layer = 3 × hidden × intermediate (SwiGLU) or 2 × hidden × intermediate (GELU MLP)"
if_increased: "More per-layer capacity to store knowledge, but the FFN is already the biggest parameter block, so cost rises steeply."
if_decreased: "Fewer parameters and less memorization capacity; the model leans harder on depth and attention."
tags: [llm, transformer, ffn, mlp, swiglu, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2002.05202", title: "GLU Variants Improve Transformer (SwiGLU, Shazeer 2020)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
---

## Definition
`intermediate_size` (`d_ffn`) is the hidden dimension of the **feed-forward network** inside each
transformer block. The FFN expands each token from `hidden_size` up to `intermediate_size`, applies a
non-linearity, then projects back down.

Because there is one FFN per layer and it is wide, the feed-forward blocks account for roughly **70%**
of a dense LLM's total parameters — making this the single largest weight bucket.

## What to look for
The ratio `intermediate_size / hidden_size` reveals the FFN design:

- **Classic GELU MLP (2 matrices):** ratio ≈ 4× (BERT, GPT-2).
- **SwiGLU (3 matrices: gate, up, down):** ratio ≈ 2.67× to keep compute comparable to a 4× GELU
  FFN, since SwiGLU has an extra matrix. Llama 3 8B uses 14336 / 4096 ≈ 3.5×.

## Calculation
- **SwiGLU:** three matrices → `3 × hidden × intermediate` parameters per layer.
- **Standard MLP:** two matrices (up + down) → `2 × hidden × intermediate` per layer.

Multiply by `num_hidden_layers` for the total FFN contribution.

## Example
Llama 3 8B: `hidden = 4096`, `intermediate = 14336`, SwiGLU. Per-layer FFN =
`3 × 4096 × 14336 ≈ 176.2M`. Across 32 layers that is ≈5.64B — about 70% of the 8.03B total,
dwarfing the ≈1.34B spent on attention. In a Mixture-of-Experts model this same expert-FFN block is
replicated per expert, which is why expert count dominates MoE parameter counts.
