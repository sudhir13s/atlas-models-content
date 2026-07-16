---
id: parameter/llm-transformer
type: parameter
slug: llm-transformer
schema_version: 1.0
title: "LLM / Transformer Parameters"
arch: llm
order: 1
color_key: llm
components:
  - { name: "num_hidden_layers (depth)", rank: 1, importance: critical, badge: "Structure", formula: "layers × per-layer params", range: "12 (small) → 80 (70B)" }
  - { name: "hidden_size (d_model, width)", rank: 2, importance: critical, badge: "Structure", formula: "must be divisible by num_heads", range: "768 → 8192" }
  - { name: "num_attention_heads", rank: 3, importance: critical, badge: "Per Layer", formula: "head_dim = hidden_size / num_heads", range: "12 → 64" }
  - { name: "intermediate_size (FFN, SwiGLU)", rank: 4, importance: important, badge: "Per Layer", formula: "≈ 2.67 × hidden_size for SwiGLU (3 matrices)", range: "≈ 2.67–4 × hidden" }
  - { name: "num_key_value_heads (GQA)", rank: 5, importance: important, badge: "Per Layer", formula: "KV cache ∝ kv_heads", range: "1 (MQA) → num_heads (MHA)" }
  - { name: "vocab_size", rank: 6, importance: important, badge: "Embedding", formula: "embedding = vocab_size × hidden_size", range: "32k → 152k" }
  - { name: "max_position_embeddings (context)", rank: 7, importance: critical, badge: "Structure", formula: "KV cache grows linearly with used context", range: "8k → 1M" }
worked_example:
  label: "Llama 3 8B"
  config: { vocabSize: 128256, hiddenDim: 4096, ffnDim: 14336, numLayers: 32, attentionHeads: 32, kvHeads: 8, headDim: 128, normalization: RMSNorm, activation: SwiGLU, tiedEmbeddings: false }
  sources:
    - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
    - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need", trust: 0.95 }
tags: [llm, transformer, parameters]
updated: 2026-07-16
sources:
  - { url: "https://arxiv.org/abs/2002.05202", title: "GLU Variants Improve Transformer (SwiGLU)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2305.13245", title: "GQA: Grouped-Query Attention", trust: 0.9 }
---

## Overview
A dense transformer's parameter count is the sum of its embedding table, its stacked layers (each
an attention block plus a feed-forward block plus norms), a final norm, and an output head. Two
choices dominate the total: **depth** (`num_hidden_layers`) and **width** (`hidden_size`), because
every per-layer cost is multiplied by depth and scales with width. The feed-forward blocks hold the
majority of the parameters (~70% in a modern LLM).

## Components
- **Depth / width** set overall capacity; width must be divisible by the head count.
- **Attention** projects Q, K, V, O. With grouped-query attention, K and V use `kv_heads` instead of
  the full head count, shrinking the KV cache by `kv_heads / num_heads`.
- **Feed-forward (SwiGLU)** uses three matrices, so its effective expansion is ~2.67× `hidden_size`
  to keep compute comparable to a classic 4× GELU FFN.
- **Embedding / output head** are `vocab_size × hidden_size` each (shared if tied).

The master formula for a dense transformer:

$$\text{Total} = V d + L\big[(Q{+}K{+}V{+}O) + 3 d\,d_\text{ffn} + 2d\big] + d + V d$$

where $V$ = vocab, $d$ = hidden size, $d_\text{ffn}$ = intermediate size, $L$ = layers (output head
term is 0 if embeddings are tied).

## Worked Example
Plugging in Llama 3 8B (`config` above): embedding 128256×4096 ≈ 525.3M; per-layer attention
(Q 16.8M + K 4.2M + V 4.2M + O 16.8M) ≈ 41.9M; per-layer SwiGLU FFN 3×4096×14336 ≈ 176.2M; per-layer
norms 8,192; ×32 layers ≈ 6.98B; plus an untied output head 525.3M and a final norm → **≈8.03B
parameters**, matching the reported total. The FFN blocks account for roughly 70% of the count.
