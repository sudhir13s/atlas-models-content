---
id: parameter-calculator/llm-transformer
type: parameter-calculator
slug: llm-transformer
schema_version: 3.1
title: LLM / Transformer Parameters
arch: llm
order: 1
color_key: llm
components:
- name: num_hidden_layers (depth)
  rank: 1
  importance: critical
  badge: Structure
  formula: total = layers × per-layer params
  range: 12 (small) → 80 (70B)
- name: hidden_size (d_model, width)
  rank: 2
  importance: critical
  badge: Structure
  formula: must be divisible by num_heads; scales every block
  range: 768 → 8192
- name: FFN gate/up/down (SwiGLU, 3 matrices)
  rank: 3
  importance: critical
  badge: Per Layer
  formula: 3 × hidden × intermediate; ~70% of all params
  range: intermediate ≈ 2.67–4 × hidden
- name: token embedding table
  rank: 4
  importance: critical
  badge: Embedding
  formula: vocab_size × hidden_size
  range: V = 32k → 152k
- name: attention query projection (Wq)
  rank: 5
  importance: critical
  badge: Per Layer
  formula: hidden × (num_heads × head_dim) = d²
  range: ≈ d²
- name: attention output projection (Wo)
  rank: 6
  importance: critical
  badge: Per Layer
  formula: (num_heads × head_dim) × hidden = d²
  range: ≈ d²
- name: attention key/value projections (Wk, Wv)
  rank: 7
  importance: important
  badge: Per Layer
  formula: 2 × hidden × (kv_heads × head_dim); GQA shrinks by kv_heads/num_heads
  range: d² (MHA) → small (MQA)
- name: num_attention_heads / head_dim
  rank: 8
  importance: critical
  badge: Per Layer
  formula: head_dim = hidden_size / num_heads
  range: 12 → 128 heads
- name: num_key_value_heads (GQA / MQA)
  rank: 9
  importance: important
  badge: Per Layer
  formula: KV cache ∝ kv_heads
  range: 1 (MQA) → num_heads (MHA)
- name: output / unembedding head
  rank: 10
  importance: important
  badge: Head
  formula: vocab_size × hidden_size (0 if tied)
  range: 0 or V·d
- name: tied vs untied embeddings
  rank: 11
  importance: architectural
  badge: Structure
  formula: tying reuses the embedding as the head, saving V·d
  range: tie (small) vs untie (large)
- name: positional encoding (RoPE / learned / ALiBi)
  rank: 12
  importance: architectural
  badge: Per Model
  formula: RoPE & ALiBi = 0 learned params; learned absolute = max_pos × hidden
  range: 0 → max_pos·d
- name: RMSNorm / LayerNorm (pre/post + final)
  rank: 13
  importance: optional
  badge: Per Layer
  formula: RMSNorm = d per norm (LayerNorm = 2d); ≈ (2L+1)·d total
  range: tiny
- name: attention & FFN biases (optional)
  rank: 14
  importance: optional
  badge: Per Layer
  formula: usually 0 in modern LLMs (Llama/Mistral drop them)
  range: 0 (typical)
worked_example:
  label: Llama 3 8B
  config:
    vocabulary_size: 128256
    hidden_size: 4096
    intermediate_size: 14336
    num_layers: 32
    attention_heads: 32
    kv_heads: 8
    head_size: 128
    normalization: RMSNorm
    activation_function: SwiGLU
    tiedEmbeddings: false
  sources:
  - url: https://arxiv.org/abs/2407.21783
    title: The Llama 3 Herd of Models
    trust: 0.9
  - url: https://arxiv.org/abs/1706.03762
    title: Attention Is All You Need
    trust: 0.95
tags:
- llm
- transformer
- parameters
updated: '2026-07-17'
sources:
- url: https://arxiv.org/abs/2002.05202
  title: GLU Variants Improve Transformer (SwiGLU)
  trust: 0.9
- url: https://arxiv.org/abs/2305.13245
  title: 'GQA: Grouped-Query Attention'
  trust: 0.9
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
