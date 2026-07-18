---
id: parameter/activation-function
type: parameter
slug: activation-function
schema_version: 1.0
title: "Activation Function (SwiGLU / GELU)"
name: hidden_act
priority: 12
importance: optional
applies_to: [llm, transformer, moe]
range: "SwiGLU · GELU · GEGLU · ReLU"
controls: "The non-linearity inside the feed-forward block. Gated variants (SwiGLU) change how many matrices the FFN has, and therefore its parameter count."
formula: "gated (SwiGLU/GEGLU) FFN = 3 × hidden × intermediate; plain (GELU/ReLU) FFN = 2 × hidden × intermediate"
if_increased: "(Toward gated) SwiGLU adds a third matrix (a gate), improving quality per FLOP; teams shrink intermediate_size to keep the parameter budget flat."
if_decreased: "(Toward plain GELU) two matrices, simpler and slightly fewer params at equal width, but usually a small quality drop."
tags: [llm, transformer, activation, swiglu, gelu, ffn, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2002.05202", title: "GLU Variants Improve Transformer (SwiGLU, Shazeer 2020)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1606.08415", title: "Gaussian Error Linear Units (GELU, Hendrycks & Gimpel, 2016)", trust: 0.85 }
---

## Definition
The **activation function** (`hidden_act`) is the non-linearity applied inside each feed-forward
block. It matters structurally because **gated** activations change the FFN's shape:

- **Plain MLP** (ReLU, GELU): two matrices — up-project then down-project.
- **Gated GLU** (SwiGLU, GEGLU): three matrices — a **gate** and an **up** projection multiplied
  element-wise, then a down projection.

## What to look for
SwiGLU is the modern default (Llama, Mistral, PaLM, Qwen) because it improves quality per FLOP. The
catch is the extra matrix: to keep the parameter and compute budget comparable to a classic 4×-GELU
FFN, models set `intermediate_size ≈ 2.67× hidden_size` instead of 4×.

So the activation choice is inseparable from `intermediate_size` when reasoning about FFN size.

## Calculation
$$\text{FFN params per layer} = \begin{cases} 3 \times \text{hidden} \times \text{intermediate} & \text{gated (SwiGLU/GEGLU)} \\ 2 \times \text{hidden} \times \text{intermediate} & \text{plain (GELU/ReLU)} \end{cases}$$

## Example
Llama 3 8B uses **SwiGLU** with `hidden = 4096`, `intermediate = 14336`: three matrices →
`3 × 4096 × 14336 ≈ 176.2M` per layer. A GELU MLP matching that compute would use a ~4× intermediate
(`≈16384`) but only two matrices → `2 × 4096 × 16384 ≈ 134M` — fewer FFN parameters, typically at a
small quality cost.
