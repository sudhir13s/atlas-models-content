---
id: architecture-era/flashattention
type: architecture-era
slug: flashattention
schema_version: 1.0
title: "FlashAttention"
name: "FlashAttention"
era: post-transformer
era_range: "2022–2024"
year: 2022
family: llm
core_innovation: "An IO-aware, tiled attention kernel that computes exact attention without materializing the full N×N matrix in slow memory."
description: "The GPU-memory-aware attention kernel that made long-context transformers fast and memory-cheap."
key_features: ["IO-aware tiling", "Online softmax (no full score matrix)", "Fused kernel, fewer HBM reads/writes", "Exact (not approximate) attention", "Linear memory in sequence length"]
key_models: ["FlashAttention (2022)", "FlashAttention-2 (2023)", "long-context LLMs (2023+)"]
paper_title: "FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness"
paper_url: "https://arxiv.org/abs/2205.14135"
influenced_by: [architecture-era/transformer]
led_to: []
tags: [flashattention, attention, gpu, systems, long-context]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2205.14135", title: "FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness (Dao et al., 2022)", trust: 0.95 }
---

## What Changed
This entry is a *systems* advance, not a new model architecture — but it reshaped what transformers can
do, so it belongs in the timeline. Standard attention computes an $N \times N$ score matrix, applies
softmax, and multiplies by values. The bottleneck is not the arithmetic but the **memory traffic**:
that giant intermediate matrix is written to and read from the GPU's slow high-bandwidth memory (HBM),
and its $O(N^2)$ size caps how long a sequence you can process. FlashAttention makes attention
**IO-aware**. It tiles the computation into blocks that fit in fast on-chip SRAM, uses an **online
softmax** to accumulate the result block by block without ever storing the full score matrix, and
fuses everything into one kernel — dramatically cutting HBM reads/writes. The result is the *exact*
same attention output (no approximation), but faster and with memory that grows *linearly*, not
quadratically, in sequence length. That single kernel is a big part of why context windows jumped from
a couple thousand tokens to tens or hundreds of thousands. It sits on the transformer branch as
critical enabling infrastructure rather than a change to the model itself.

## Key Ideas
- **IO-awareness** — the true cost of attention on a GPU is memory movement, not FLOPs; optimising
  HBM traffic is what wins, an insight from hardware, not model design.
- **Tiling + online softmax** — process attention in SRAM-sized blocks and update a running softmax so
  the full $N\times N$ matrix never needs to exist.
- **Exact, not approximate** — unlike sparse/linear attention variants, FlashAttention returns the
  identical result to standard attention, so it is a drop-in speed/memory win.
- **Enables long context** — linear memory in sequence length is a primary reason modern LLMs handle
  very long inputs; the advance is infrastructure, not architecture.
