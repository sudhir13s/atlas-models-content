---
id: parameter/num-hidden-layers
type: parameter
slug: num-hidden-layers
schema_version: 1.0
title: "Number of Hidden Layers (Depth)"
name: num_hidden_layers
priority: 1
importance: critical
applies_to: [llm, transformer, moe]
range: "12 (small) → 80+ (70B-class)"
controls: "How many transformer blocks are stacked — the model's depth. Every per-layer cost is multiplied by this number."
formula: "total ≈ vocab·d + L·(attention + FFN + norms) + output head; depth L multiplies the whole per-layer term"
if_increased: "More representational depth and capacity, but linearly more parameters, more compute per token, and harder optimization (vanishing signal without good residuals/norms)."
if_decreased: "Cheaper and faster, but less capacity for multi-step composition; very shallow models plateau on hard reasoning."
tags: [llm, transformer, depth, structure, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2001.08361", title: "Scaling Laws for Neural Language Models (Kaplan et al.)", trust: 0.9 }
---

## Definition
`num_hidden_layers` is the count of identical transformer blocks stacked on top of each other — the
model's **depth** (`L`). Each block is a self-attention sub-layer plus a feed-forward sub-layer,
wrapped in residual connections and normalization.

Depth is one of the two dominant levers on parameter count (the other is `hidden_size`), because
every per-layer cost is multiplied by `L`.

## What to look for
Depth and width should scale together — a very deep, very narrow model optimizes poorly, and a very
wide, shallow one wastes capacity. Common shapes:

- **Small (≈100M–1B):** 12–24 layers.
- **Mid (7B–8B):** 28–32 layers (Llama 3 8B uses 32).
- **Large (65B–70B):** 80 layers (Llama 3 70B).

Watch training stability: as `L` grows you rely more on pre-norm placement and residual scaling to
keep the signal from vanishing or exploding.

## Calculation
Depth multiplies the entire per-layer block:

$$\text{Total} \approx V d + L\big[(Q{+}K{+}V{+}O) + 3\,d\,d_\text{ffn} + 2d\big] + d + V d$$

where `V` = vocab, `d` = hidden size, `d_ffn` = intermediate size. Doubling `L` roughly doubles
everything inside the bracket (the bulk of the model), leaving only the embedding and output-head
terms fixed.

## Example
Llama 3 8B: `L = 32`, `d = 4096`, `d_ffn = 14336`. The per-layer block is ≈218M parameters
(≈42M attention + ≈176M SwiGLU FFN). Over 32 layers that is ≈6.98B — the overwhelming majority of
the model's 8.03B total. Cut depth to 16 layers and the model drops to roughly 4.5B parameters.
