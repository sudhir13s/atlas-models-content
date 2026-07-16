---
id: architecture-era/transformer
type: architecture-era
slug: transformer
schema_version: 1.0
title: "Transformer"
name: "Transformer"
era: scaling
era_range: "2017–2022"
year: 2017
family: llm
core_innovation: "Self-attention replaces recurrence, enabling fully parallel sequence modeling."
description: "The attention-only sequence architecture that became the substrate of every modern LLM."
key_features: ["Scaled dot-product self-attention", "Multi-head attention", "Positional encoding", "Fully parallel training"]
key_models: ["BERT (2018)", "GPT-2/3 (2019–2020)", "T5 (2019)", "ViT (2020)"]
paper_title: "Attention Is All You Need"
paper_url: "https://arxiv.org/abs/1706.03762"
influenced_by: []
led_to: [architecture-era/mixture-of-experts]
tags: [transformer, attention, llm, foundational]
updated: 2026-07-16
sources:
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al.)", trust: 0.95 }
---

## What Changed
The transformer removed recurrence entirely. Where RNNs and LSTMs processed a sequence step by step
(and struggled to parallelise), the transformer computes attention over all positions at once. This
made training fully parallel across the sequence, unlocked scaling to billions of parameters, and
mixed long-range dependencies directly rather than through a compressed hidden state. It grew out of
the encoder–decoder attention line (seq2seq → additive attention) and led directly to BERT, the GPT
family, Vision Transformers, and later Mixture-of-Experts variants.

## Key Ideas
- **Scaled dot-product attention** — each token attends to every other via query/key/value
  projections, scaled by $1/\sqrt{d_k}$.
- **Multi-head attention** — several attention patterns run in parallel per layer, then concatenate.
- **Positional encoding** — since attention is order-agnostic, position is injected (originally
  sinusoidal; modern models use RoPE).
- **Residual + normalization** — each sublayer is wrapped in a residual connection and normalization,
  enabling deep stacks.
