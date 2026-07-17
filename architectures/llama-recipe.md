---
id: architecture-era/llama-recipe
type: architecture-era
slug: llama-recipe
schema_version: 1.0
title: "The LLaMA Recipe"
name: "LLaMA (Modern Open LLM Recipe)"
era: post-transformer
era_range: "2023–2026"
year: 2023
family: llm
core_innovation: "A consolidated, efficient decoder-only recipe — RMSNorm, SwiGLU, RoPE, GQA — trained data-optimally and released openly."
description: "The reference open-weights transformer recipe that standardized modern LLM design and catalyzed the open ecosystem."
key_features: ["Pre-norm with RMSNorm", "SwiGLU feed-forward", "Rotary position embeddings (RoPE)", "Grouped-query attention (Llama-2)", "Chinchilla-informed data scaling", "Open weights"]
key_models: ["LLaMA (2023)", "Llama-2 (2023)", "Llama-3 (2024)"]
paper_title: "LLaMA: Open and Efficient Foundation Language Models"
paper_url: "https://arxiv.org/abs/2302.13971"
influenced_by: [architecture-era/transformer, architecture-era/scaling-laws]
led_to: [architecture-era/mixture-of-experts, architecture-era/mamba-ssm]
tags: [llama, open-weights, rope, rmsnorm, swiglu, gqa, llm]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2302.13971", title: "LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2307.09288", title: "Llama 2: Open Foundation and Fine-Tuned Chat Models (Touvron et al., 2023)", trust: 0.9 }
---

## What Changed
By 2023 the transformer's best-practice details were scattered across many papers. LLaMA bundled them
into one clean, efficient, *reproducible* decoder-only recipe and — crucially — released the weights,
igniting the open-model ecosystem. The recipe is a set of well-chosen refinements over the original
transformer block: **RMSNorm** (a cheaper normalization) applied **pre-norm** for training stability;
**SwiGLU** gated feed-forward layers that outperform plain ReLU MLPs; **RoPE** (rotary position
embeddings) that encode relative position by rotating query/key vectors and generalise better to
longer contexts than learned absolute embeddings; and, in Llama-2, **grouped-query attention (GQA)**,
which shares key/value heads across groups of query heads to shrink the KV-cache and speed up
inference. It also took the Chinchilla lesson seriously, training smaller models on far more tokens so
that a 13B model could rival a 175B one. This combination became the de facto template that most
open-weight LLMs now follow, and its efficient backbone is what later MoE and state-space variants
extend.

## Key Ideas
- **RMSNorm, pre-norm** — normalise by root-mean-square (no mean subtraction) before each sublayer;
  cheaper and more stable for deep stacks than post-norm LayerNorm.
- **SwiGLU FFN** — a gated activation ($\text{Swish}(xW)\odot xV$) that consistently beats ReLU MLPs at
  equal compute, improving quality per parameter.
- **RoPE** — rotate query/key vectors by position so attention scores depend on *relative* offset,
  giving better length behaviour than absolute position embeddings.
- **Grouped-query attention** — share K/V projections across query-head groups to cut KV-cache memory
  and boost inference throughput with minimal quality loss.
- **Data-optimal + open** — train longer on more tokens per Chinchilla, and release weights so the
  community can fine-tune and extend the recipe.
