---
id: parameter/num-key-value-heads-gqa
type: parameter
slug: num-key-value-heads-gqa
schema_version: 2.9
title: "Number of Key/Value Heads (GQA / MQA)"
name: num_key_value_heads
priority: 8
importance: important
applies_to: [llm, transformer, moe]
range: "1 — 8"
controls: "How many key-value heads each attention layer has; groups of query heads share one, which sets the size of the KV cache."
effects:
  decrease:
    - "A smaller KV cache, in a straight line: Qwen3-32B caches 256 KiB per token with 8, against 2 MiB with 64"
    - "Smaller key and value projections, by the same factor"
    - "In the GQA paper, multi-query attention (1 head) averaged 46.6 against 47.2 for multi-head attention"
  increase:
    - "A larger KV cache and more memory read at every generated token"
    - "In the GQA paper, 8 groups averaged 47.1, close to multi-head attention's 47.2"
    - "In the same table, time per sample 0.28 with 8 groups against 1.51 with multi-head attention"
scale:
  min: 1
  max: 128
  typical_min: 1
  typical_max: 8
  spacing: log
typical_values:
  - setting: "PaLM, multi-query attention (named in the GQA paper)"
    value: "1"
    source: "https://arxiv.org/abs/2305.13245"
  - setting: "SmolLM2-135M, with 9 query heads (config.json)"
    value: "3"
    source: "https://huggingface.co/HuggingFaceTB/SmolLM2-135M/blob/main/config.json"
  - setting: "Qwen3-30B-A3B, with 32 query heads (config.json)"
    value: "4"
    source: "https://huggingface.co/Qwen/Qwen3-30B-A3B/blob/main/config.json"
  - setting: "Qwen3-8B, with 32 query heads (config.json)"
    value: "8"
    source: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json"
  - setting: "Qwen3-32B, with 64 query heads (config.json)"
    value: "8"
    source: "https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json"
  - setting: "T5-XXL uptrained to GQA-8, the paper's main model (Ainslie et al.)"
    value: "8"
    source: "https://arxiv.org/abs/2305.13245"
mistakes:
  - mistake: "Assuming fewer key-value heads means fewer attention heads"
    why: >-
      The query heads stay as they are. Ainslie et al. define grouped-query attention as query heads
      divided into **G groups**, each sharing one key head and one value head. Qwen3-32B keeps all
      **64** query heads; only the keys and values come in **8** heads.
    fix: "Read num_attention_heads for the query heads and num_key_value_heads for the keys and values."
  - mistake: "Sizing the KV cache from num_attention_heads"
    why: >-
      The cache holds keys and values, so it scales with the key-value heads. Ainslie et al. note that
      going from multi-head to multi-query attention cuts the KV cache by a factor of **H**, the head
      count. For Qwen3-32B that is **256 KiB** per token, not the **2 MiB** that 64 heads would need.
    fix: "Compute the cache from num_key_value_heads, as in the worked example."
  - mistake: "Treating multi-query attention as free"
    why: >-
      The GQA paper reports that MQA can lead to quality degradation and training instability. In its
      Table 1, the uptrained MQA model averaged **46.6**, GQA-8 **47.1** and multi-head attention
      **47.2**; its appendix reports loss spikes when training MQA models from scratch.
    fix: "Compare the published results for the head count a model uses, not only its speed."
related: [parameter/num-attention-heads, parameter/head-dim, parameter/context-length]
visual:
  kind: kv_cache
  model: { name: "Qwen3-32B", vocab_size: 151936, hidden_size: 5120, intermediate_size: 25600, num_hidden_layers: 64, num_attention_heads: 64, num_key_value_heads: 8, head_dim: 128, tie_word_embeddings: false, ffn_matrices: 3, qk_norm: true }
  bytes_per_value: 2
  tokens: 32768
  caption: "The keys and values Qwen3-32B caches, computed from its config.json with 16-bit numbers (2 bytes each): per token, and for its native context of 32,768 tokens from its card. The second bar is the same model with one key-value head per query head (64), which Qwen3-32B does not have."
tags: [llm, transformer, attention, gqa, mqa, kv-cache, parameters]
updated: 2026-11-23
sources:
  - { url: "https://arxiv.org/abs/2305.13245", title: "GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints (Ainslie et al., 2023)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1911.02150", title: "Fast Transformer Decoding: One Write-Head is All You Need (Shazeer, 2019)", trust: 0.85 }
  - { url: "https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json", title: "Qwen3-32B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-32B", title: "Qwen3-32B model card", trust: 0.9 }
  - { url: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json", title: "Qwen3-8B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-30B-A3B/blob/main/config.json", title: "Qwen3-30B-A3B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/HuggingFaceTB/SmolLM2-135M/blob/main/config.json", title: "SmolLM2-135M configuration (config.json)", trust: 0.95 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py", title: "Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py", title: "Hugging Face Transformers source: modeling_qwen3.py", trust: 0.9 }
---

## Definition

**num_key_value_heads is how many key-value heads each attention layer has.**
Transformers describes it as the number of key-value heads used to implement grouped-query
attention, and defaults it to num_attention_heads.

- **Keys and values** are what each token offers to the others; **queries** are what a token
  looks for. Each head has its own of all three.
- **Equal to the query heads:** multi-head attention (MHA), every query head with its own keys and
  values.
- **1:** multi-query attention (MQA), proposed by Shazeer: all query heads share one key head and
  one value head.
- **In between:** grouped-query attention (GQA), from Ainslie et al.: each group of query heads
  shares one key-value head.
- **The figure** shows why it matters: Qwen3-32B caches **8×** less with its 8 key-value heads.

**Analogy:** a class split into tables, each table sharing one textbook instead of every pupil
having a copy. The analogy shows the idea only; the exact counts are under Calculation.

## How It Works

**Project, share, attend.**

1. **Project.** The query projection makes num_attention_heads queries per token; the key and value
   projections make only num_key_value_heads keys and values.
2. **Share.** In Transformers' Qwen3 code, each key-value head is repeated for its group of
   num_attention_heads ÷ num_key_value_heads query heads.
3. **Attend.** Every query head then attends as usual, so the number of attention patterns stays
   the same.
4. **Cache.** While generating, the model keeps the keys and values of every earlier token. Ainslie
   et al. name loading them at every step as the bandwidth cost that MQA and GQA reduce.

**Where GQA came from:** Ainslie et al. converted multi-head checkpoints by mean-pooling the key and
value heads of each group, then trained further for **5%** of the original steps.

## Calculation

**How the key-value heads enter the parameters and the cache.**

In words: the key and value projections each connect hidden_size to key-value heads × head_dim
numbers; the cache stores a key and a value per key-value head, per layer, per token.

$$
K = V = d \times (n_{kv}\,h) \qquad \text{cache per token} = 2\,L\,n_{kv}\,h\,b
$$

| Symbol | Meaning |
| --- | --- |
| $d$ | hidden_size |
| $n_{kv}$ | num_key_value_heads |
| $h$ | head_dim |
| $L$ | num_hidden_layers |
| $b$ | bytes per cached number: 2 in 16-bit |

- **Both grow in a straight line** with $n_{kv}$; the query and output projections do not change.
- For whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Qwen3-32B, from its config.json** (the numbers on its catalog page): hidden_size **5120**, **64**
query heads, **8** key-value heads, head_dim **128**, **64** layers.

1. **Group size:** 64 ÷ 8 = **8** query heads share each key-value head.
2. **Key projection:** 5120 × (8 × 128) = 5120 × 1024 = **5,242,880**; the value projection is the
   same size.
3. **With 64 key-value heads instead:** 5120 × 8192 = **41,943,040** each.
4. **Saving per layer:** 2 × (41,943,040 − 5,242,880) = **73,400,320**; over 64 layers,
   **4,697,620,480** parameters.
5. **Cache per token:** 2 × 64 × 8 × 128 × 2 = **262,144** bytes = **256 KiB**.
6. **At the card's native 32,768 tokens:** 262,144 × 32,768 bytes = **8 GiB**, against **64 GiB**
   with 64 key-value heads.

**What this means:** Qwen3-32B keeps its 64 query heads but stores keys and values for 8, which
makes its cache 8 times smaller and its total 32,762,123,264 parameters instead of 37,459,743,744.

## What To Watch

- **The ratio to num_attention_heads.** It gives the group size: 4 in Qwen3-8B, 8 in Qwen3-32B and
  Qwen3-30B-A3B.
- **A missing key.** Transformers then uses num_attention_heads, which means multi-head attention.
- **head_dim.** The cache scales with it too; see the head dimension page.
