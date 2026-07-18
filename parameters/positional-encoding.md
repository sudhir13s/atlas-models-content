---
id: parameter/positional-encoding
type: parameter
slug: positional-encoding
schema_version: 1.0
title: "Positional Encoding (RoPE / Learned / ALiBi)"
name: position_embedding_type
priority: 11
importance: optional
applies_to: [llm, transformer, moe]
range: "RoPE · learned-absolute · ALiBi · sinusoidal"
controls: "How the model injects word-order information into an otherwise permutation-invariant attention mechanism."
formula: "learned absolute = max_position × hidden params; RoPE, ALiBi, sinusoidal = 0 params"
if_increased: "(Learned) more positions add a bigger position table; switching to RoPE/ALiBi removes those parameters and generalizes better to longer sequences."
if_decreased: "Fewer position parameters, but a scheme that extrapolates poorly caps usable context length."
tags: [llm, transformer, positional-encoding, rope, alibi, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2104.09864", title: "RoFormer: Enhanced Transformer with Rotary Position Embedding (Su et al., 2021)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2108.12409", title: "Train Short, Test Long: Attention with Linear Biases (ALiBi, Press et al., 2021)", trust: 0.85 }
---

## Definition
**Positional encoding** is how a transformer learns *order*. Self-attention is permutation-invariant
on its own, so position must be added explicitly. The main schemes:

- **Learned absolute** — a trainable vector per position (GPT-2, BERT).
- **Sinusoidal** — fixed sine/cosine functions (original Transformer).
- **RoPE (rotary)** — rotates Q and K by a position-dependent angle (Llama, Mistral, Qwen).
- **ALiBi** — adds a distance penalty to attention scores.

## What to look for
The choice is mostly about **extrapolation and parameters**, not accuracy on trained lengths:

- RoPE and ALiBi add **zero** parameters and extrapolate to longer contexts (RoPE with scaling tricks
  underlies most long-context models today).
- Learned absolute positions cost `max_position × hidden` parameters and do not extend past the
  trained length.

Modern LLMs almost universally use **RoPE**.

## Calculation
$$\text{params} = \begin{cases} \text{max\_position} \times d & \text{learned absolute} \\ 0 & \text{RoPE, ALiBi, sinusoidal} \end{cases}$$

## Example
Llama 3 uses **RoPE**, contributing no parameters while supporting a 128k context via RoPE frequency
scaling. GPT-2, by contrast, spent `1024 × 768 ≈ 0.79M` on a learned position table that hard-capped
it at 1024 tokens.
