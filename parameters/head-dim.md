---
id: parameter/head-dim
type: parameter
slug: head-dim
schema_version: 2.9
title: "Head Dimension"
name: head_dim
priority: 7
importance: important
applies_to: [llm, transformer, moe]
range: "64 — 256"
controls: "How many numbers each attention head works with: the length of one head's query, key and value vectors."
effects:
  decrease:
    - "Smaller query, key and value projections when the head counts stay the same"
    - "A smaller KV cache: it grows in proportion to head_dim"
    - "In the original Transformer's tests, key size 16 scored 25.1 BLEU against 25.8 at 64"
  increase:
    - "Larger projections when the head counts stay the same"
    - "A larger KV cache per token, in proportion"
    - "Each head compares tokens in a larger space"
scale:
  min: 16
  max: 512
  typical_min: 64
  typical_max: 256
  spacing: log
typical_values:
  - setting: "Original Transformer, base model: 512 ÷ 8 heads (Vaswani et al.)"
    value: "64"
    source: "https://arxiv.org/abs/1706.03762"
  - setting: "BERT-base: 768 ÷ 12 heads (config.json sets no head_dim)"
    value: "64"
    source: "https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json"
  - setting: "Qwen3-8B, Qwen3-32B and Qwen3-0.6B (config.json)"
    value: "128"
    source: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json"
  - setting: "Gemma 2 27B (Gemma 2 report, Table 1)"
    value: "128"
    source: "https://arxiv.org/abs/2408.00118"
  - setting: "Gemma 2 2B and 9B (Gemma 2 report, Table 1)"
    value: "256"
    source: "https://arxiv.org/abs/2408.00118"
mistakes:
  - mistake: "Computing head_dim as hidden_size ÷ heads for every model"
    why: >-
      Transformers uses hidden_size ÷ num_attention_heads only when the configuration sets no
      head_dim. Qwen3 sets it: **Qwen3-32B** has 5120 ÷ 64 = 80 by division, but head_dim **128**;
      **Qwen3-0.6B** has 1024 ÷ 16 = 64 by division, but head_dim **128**.
    fix: "Read head_dim from config.json when it is there."
  - mistake: "Shrinking head_dim to fit more heads at no cost"
    why: >-
      Vaswani et al. found that reducing the attention key size hurt quality: in their Table 3,
      rows B, key size **16** scored **25.1** BLEU and **32** scored **25.4**, against **25.8** for
      the base model's 64.
    fix: "Change head_dim only with an evaluation; the models listed here use 64 to 256."
  - mistake: "Sizing the KV cache by the query heads"
    why: >-
      Only keys and values are cached, one pair per key-value head. Qwen3-8B caches 8 heads of 128,
      not 32: **144 KiB** per token in 16-bit numbers instead of **576 KiB**. Ainslie et al. note
      that going from multi-head to multi-query attention shrinks the cache by the number of heads.
    fix: "Use num_key_value_heads in the cache formula, as in the worked example."
related: [parameter/num-attention-heads, parameter/hidden-size, parameter/num-key-value-heads-gqa, parameter/context-length]
visual:
  kind: kv_cache
  model: { name: "Qwen3-8B", vocab_size: 151936, hidden_size: 4096, intermediate_size: 12288, num_hidden_layers: 36, num_attention_heads: 32, num_key_value_heads: 8, head_dim: 128, tie_word_embeddings: false, ffn_matrices: 3, qk_norm: true }
  bytes_per_value: 2
  tokens: 32768
  caption: "The keys and values Qwen3-8B caches, computed from its config.json with 16-bit numbers (2 bytes each): per token, and for its native context of 32,768 tokens. The second bar is the same model with one key-value head per query head, which Qwen3-8B does not have."
tags: [llm, transformer, attention, head-dim, parameters]
updated: 2026-11-23
sources:
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json", title: "Qwen3-8B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-8B", title: "Qwen3-8B model card", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py", title: "Hugging Face Transformers source: modeling_qwen3.py", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py", title: "Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2305.13245", title: "GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints (Ainslie et al., 2023)", trust: 0.9 }
  - { url: "https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json", title: "Qwen3-32B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-0.6B/blob/main/config.json", title: "Qwen3-0.6B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json", title: "BERT-base uncased configuration (config.json)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2408.00118", title: "Gemma 2: Improving Open Language Models at a Practical Size (Gemma Team, 2024)", trust: 0.9 }
---

## Definition

**head_dim is the number of numbers each attention head works with.**
Transformers describes it as "the attention head dimension", defaulting to hidden_size ÷
num_attention_heads when the configuration leaves it out.

- **Each head** turns a token's vector into a query, a key and a value of head_dim numbers each.
- **Scores** between tokens are dot products of queries and keys of this length; Vaswani et al.
  divide them by √head_dim (their d_k) so they do not grow too large.
- **Keys and values are cached** while a model writes text, head_dim numbers per key-value head,
  per layer, per token.
- **The figure** shows that cache for Qwen3-8B: **144 KiB** per token, **4.5 GiB** at 32,768
  tokens.

**Analogy:** the size of the form each reader fills in for every word. A bigger form holds more
detail and takes more filing space for every word read. The analogy shows the idea only.

## How It Works

**head_dim sets the width of every head's vectors.**

1. **Query.** The query projection makes num_attention_heads vectors of head_dim numbers.
2. **Key and value.** The key and value projections make num_key_value_heads vectors of head_dim
   numbers each.
3. **Score.** Each query is compared with each key by a dot product, divided by √head_dim.
4. **Cache.** When text is generated one token at a time, each new token's keys and values are
   kept, so earlier tokens are not recomputed.
5. **Output.** The heads' results, heads × head_dim numbers, are projected back to hidden_size.

## Calculation

**The KV cache per token** (keys and values, every layer, every key-value head).

In words: 2 (a key and a value) times layers times key-value heads times head_dim, times the bytes
of one number.

$$
\text{bytes per token} = 2 \times L \times n_{kv} \times h \times b
$$

| Symbol | Meaning |
| --- | --- |
| $L$ | num_hidden_layers |
| $n_{kv}$ | num_key_value_heads |
| $h$ | head_dim |
| $b$ | bytes per number: 2 in 16-bit |

head_dim also sizes the projections: $Q$ and $O$ are $d \times (n_h h)$, $K$ and $V$ are
$d \times (n_{kv} h)$. For whole-model counts, see the
[parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**The KV cache of Qwen3-8B, from its config.json** (the numbers on its catalog page): 36 layers,
8 key-value heads, head_dim **128**, stored as 16-bit numbers of 2 bytes.

1. **Numbers per token:** 2 × 36 × 8 × 128 = **73,728**.
2. **Bytes per token:** 73,728 × 2 = **147,456** bytes, which is 147,456 ÷ 1024 = **144 KiB**.
3. **At the native 32,768 tokens:** 147,456 × 32,768 = 4,831,838,208 bytes, which is
   ÷ 1024³ = **4.5 GiB**.
4. **With 32 key-value heads,** one per query head: 4 times as much, **576 KiB** per token and
   **18 GiB** at 32,768 tokens.
5. **Division check:** 4096 ÷ 32 = 128, so here head_dim equals hidden_size ÷ heads.

**What this means:** head_dim and the key-value heads together size the cache; halving head_dim
would halve it, and grouped-query attention already divides it by 4 in this model.

## What To Watch

- **head_dim in config.json.** Qwen3 and Gemma 2 set it; older configurations such as BERT's leave
  it out, and the division applies.
- **The cache at long context.** It grows in a straight line with the number of tokens; see the
  context length page.
- **Bytes per number.** A cache stored in 8-bit numbers halves the bytes; the formula above takes
  b as a variable.
