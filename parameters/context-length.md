---
id: parameter/context-length
type: parameter
slug: context-length
schema_version: 1.0
title: "Context Length (max_position_embeddings)"
name: max_position_embeddings
priority: 10
importance: important
applies_to: [llm, transformer, moe]
range: "512 → 128k+ tokens"
controls: "The maximum sequence length the model is built and trained to attend over — how much text fits in one forward pass."
formula: "learned absolute positions add max_position_embeddings × hidden_size params; RoPE/ALiBi add 0"
if_increased: "The model can condition on more context (long documents, whole codebases), but attention compute/memory grows and the KV cache grows linearly with length."
if_decreased: "Cheaper attention and a smaller KV cache, but the model cannot see beyond a short window."
tags: [llm, transformer, context, positions, rope, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2104.09864", title: "RoFormer: Enhanced Transformer with Rotary Position Embedding (Su et al., 2021)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
---

## Definition
`max_position_embeddings` (the **context length**, `context_window`) is the longest token sequence the
model is designed to process at once. It bounds how far attention can reach and sizes the position
information the model uses.

## What to look for
Whether context length costs parameters depends on the **positional encoding**:

- **Learned absolute positions** (BERT, GPT-2) store a `max_position × hidden_size` table — so longer
  context literally adds parameters.
- **RoPE / ALiBi** (modern LLMs) encode position with a fixed function and add **zero** parameters,
  which is precisely why they enabled long-context models and context-extension tricks (RoPE scaling).

Even with RoPE, the real cost is runtime: attention is O(length²) and the **KV cache grows linearly**
with sequence length, so 128k contexts are a memory problem, not a parameter one.

## Calculation
$$\text{position params} = \begin{cases} \text{max\_position} \times d & \text{learned absolute} \\ 0 & \text{RoPE / ALiBi} \end{cases}$$

## Example
Llama 3 8B ships with a **128k** context using RoPE, so it adds **no** position parameters — the
length lives entirely in runtime attention and a KV cache that, at 128k tokens, is far larger than
the model's own activations. GPT-2 (context 1024, learned positions) instead stored a
`1024 × 768 ≈ 0.79M` position table.
