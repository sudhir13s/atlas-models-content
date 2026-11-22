---
id: parameter/normalization
type: parameter
slug: normalization
schema_version: 1.0
title: "Normalization (RMSNorm / LayerNorm)"
name: norm_type
priority: 13
importance: optional
applies_to: [llm, transformer, moe]
range: "RMSNorm · LayerNorm · pre-norm vs post-norm"
controls: "The normalization layer that stabilizes the residual stream, placed before each sub-layer (pre-norm) or after each residual add (post-norm). RMSNorm halves the per-norm parameters versus LayerNorm."
formula: "RMSNorm = d params per norm; LayerNorm = 2d per norm; total ≈ (2L + 1) × d"
if_increased: "(More/wider norms) negligible parameter change but affects training stability; extra norms rarely help."
if_decreased: "RMSNorm drops LayerNorm's bias/mean terms (d instead of 2d per norm) and is faster, with equal or better stability."
tags: [llm, transformer, normalization, rmsnorm, layernorm, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1910.07467", title: "Root Mean Square Layer Normalization (RMSNorm, Zhang & Sennrich, 2019)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1607.06450", title: "Layer Normalization (Ba et al., 2016)", trust: 0.85 }
---

## Definition
**Normalization** rescales activations to keep the residual stream well-conditioned so deep stacks
train stably. Two choices dominate transformers:

- **LayerNorm** — subtracts the mean and divides by the standard deviation, with a learned scale
  **and** bias (`2d` parameters per norm).
- **RMSNorm** — divides by the root-mean-square only, with a learned scale but **no** mean/bias
  (`d` parameters per norm). Faster and now the default (Llama, Mistral, T5).

**Placement** matters too: **pre-norm** (normalize before each sub-layer) is standard for deep models
because it keeps a clean residual path; post-norm is the original Transformer design.

## What to look for
Normalization parameters are tiny — a rounding error next to attention and FFN — so the choice is
about **stability and speed**, not budget. Each layer has two norms (pre-attention, pre-FFN) plus one
final norm before the output head.

## Calculation
$$\text{norm params} \approx \begin{cases} (2L + 1)\times d & \text{RMSNorm} \\ 2\,(2L + 1)\times d & \text{LayerNorm} \end{cases}$$

for `L` layers of hidden size `d` (two norms per layer + one final norm).

## Example
Llama 3 8B uses **RMSNorm** with `d = 4096`, `L = 32`: `(2 × 32 + 1) × 4096 ≈ 266k` parameters total
across all norms — about 0.003% of the model. Switching to LayerNorm would merely double that
negligible amount while adding bias terms and a mean subtraction.
