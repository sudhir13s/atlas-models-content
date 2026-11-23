---
id: parameter/num-attention-heads
type: parameter
slug: num-attention-heads
schema_version: 2.9
title: "Number of Attention Heads"
name: num_attention_heads
priority: 5
importance: critical
applies_to: [llm, transformer, moe]
range: "8 — 128"
controls: "How many attention heads each layer runs side by side, each comparing the tokens in its own smaller space."
effects:
  decrease:
    - "Fewer attention patterns per layer; with hidden_size fixed, each head is wider"
    - "In the original Transformer's tests at the same computation, 1 head scored 24.9 BLEU against 25.8 for 8"
    - "Fewer query parameters when head_dim stays the same"
  increase:
    - "More attention patterns per layer; with hidden_size fixed, each head is narrower"
    - "In the same tests, 32 heads of 16 scored 25.4 BLEU against 25.8 for 8 heads of 64"
    - "More query and output parameters when head_dim stays the same"
scale:
  min: 1
  max: 256
  typical_min: 8
  typical_max: 128
  spacing: log
typical_values:
  - setting: "Original Transformer, base model (Vaswani et al.)"
    value: "8"
    source: "https://arxiv.org/abs/1706.03762"
  - setting: "BERT-base (config.json)"
    value: "12"
    source: "https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json"
  - setting: "Qwen3-0.6B (config.json)"
    value: "16"
    source: "https://huggingface.co/Qwen/Qwen3-0.6B/blob/main/config.json"
  - setting: "Qwen3-8B (config.json), with 8 key-value heads"
    value: "32"
    source: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json"
  - setting: "Qwen3-32B (config.json), with 8 key-value heads"
    value: "64"
    source: "https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json"
  - setting: "Llama 3 405B (Llama 3 paper, Table 3), with 8 key-value heads"
    value: "128"
    source: "https://arxiv.org/abs/2407.21783"
mistakes:
  - mistake: "Assuming more heads always means better attention"
    why: >-
      Vaswani et al. varied the heads while keeping the computation constant (their Table 3, rows A):
      1 head scored **24.9** BLEU, 8 heads **25.8**, 16 heads **25.8** and 32 heads **25.4**. They
      write that single-head attention is 0.9 BLEU worse than the best setting, and that quality
      also drops off with too many heads.
    fix: "Treat the head count as a design choice tied to head_dim, not a dial to maximise."
  - mistake: "Assuming the head count leaves the attention parameters unchanged"
    why: >-
      That holds only when head_dim is hidden_size ÷ heads. Transformers sizes the query projection
      as hidden_size × (heads × head_dim), and Qwen3-32B sets head_dim to 128 with 64 heads: a query
      width of **8192** on a hidden_size of **5120**.
    fix: "Compute the projections from heads × head_dim, as in the worked example."
  - mistake: "Taking num_attention_heads as the number of key-value heads"
    why: >-
      With grouped-query attention, num_key_value_heads is smaller: Qwen3-8B has **32** query heads
      but **8** key-value heads. Transformers uses multi-head attention only when the two are equal,
      and multi-query attention when there is one key-value head.
    fix: "Read num_key_value_heads too; it sets the key and value projections and the KV cache."
related: [parameter/head-dim, parameter/hidden-size, parameter/num-key-value-heads-gqa]
visual:
  kind: head_grid
  model: { name: "Qwen3-8B", vocab_size: 151936, hidden_size: 4096, intermediate_size: 12288, num_hidden_layers: 36, num_attention_heads: 32, num_key_value_heads: 8, head_dim: 128, tie_word_embeddings: false, ffn_matrices: 3, qk_norm: true }
  columns: 8
  caption: "The 32 query heads of one Qwen3-8B layer, from its config.json: each is 128 numbers wide, and each group of 4 neighbouring heads reads the same key-value head, 8 in all."
tags: [llm, transformer, attention, heads, parameters]
updated: 2026-11-23
sources:
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json", title: "Qwen3-8B configuration (config.json)", trust: 0.95 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py", title: "Hugging Face Transformers source: modeling_qwen3.py", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py", title: "Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2305.13245", title: "GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints (Ainslie et al., 2023)", trust: 0.9 }
  - { url: "https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json", title: "Qwen3-32B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-0.6B/blob/main/config.json", title: "Qwen3-0.6B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json", title: "BERT-base uncased configuration (config.json)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models (Llama Team, 2024)", trust: 0.9 }
---

## Definition

**num_attention_heads is how many attention heads each layer runs side by side.**
Transformers describes the key as the "number of attention heads for each attention layer".

- **Attention** lets each token look at the other tokens and take in information from the ones
  that matter to it.
- **A head** does this in its own smaller space of head_dim numbers, with its own projections.
- **Several heads** can each follow a different pattern; Vaswani et al. write that multi-head
  attention lets the model attend to information from different representation subspaces at once.
- **The figure** lays out Qwen3-8B's **32** query heads, each **128** numbers wide.

**Analogy:** several readers going through the same page, each looking for something different,
then pooling their notes. The analogy shows the idea only; the exact rule is under Calculation.

## How It Works

**Project into heads, attend in each, join the results.**

1. **Project.** The query projection turns each token's vector into num_attention_heads queries of
   head_dim numbers each.
2. **Keys and values.** These have num_key_value_heads heads; with grouped-query attention several
   query heads share one key-value head (Ainslie et al.).
3. **Attend.** Each query head scores every earlier token, turns the scores into weights with a
   softmax, and averages the values with them.
4. **Join.** The heads' outputs, heads × head_dim numbers, are put side by side and projected back
   to hidden_size by the output projection.

Vaswani et al. used **8** heads of **64** on a d_model of 512, and note that the total computation
is then similar to one head of full width.

## Calculation

**How the head count enters the attention parameters.**

In words: the query and output projections each connect hidden_size to heads × head_dim numbers;
the key and value projections connect hidden_size to key-value heads × head_dim.

$$
Q = O = d \times (n_h h) \qquad K = V = d \times (n_{kv} h)
$$

| Symbol | Meaning |
| --- | --- |
| $d$ | hidden_size |
| $n_h$ | num_attention_heads |
| $n_{kv}$ | num_key_value_heads |
| $h$ | head_dim |

- **When $h = d \div n_h$,** $Q$ and $O$ are $d^2$ each whatever the head count.
- **When head_dim is set on its own,** more heads mean larger $Q$ and $O$.
- For whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**One layer of Qwen3-8B, from its config.json** (the numbers on its catalog page): hidden_size
4096, **32** query heads, **8** key-value heads, head_dim **128**.

1. **Query width:** 32 × 128 = **4,096**, here equal to hidden_size.
2. **Sharing:** 32 ÷ 8 = **4** query heads read each key-value head.
3. **Query projection:** 4096 × 4096 = **16,777,216**; the output projection is the same size.
4. **Key projection:** 4096 × (8 × 128) = 4096 × 1024 = **4,194,304**; the value projection is the
   same size.
5. **If every query head had its own key-value head** (32 of them): the key and value projections
   would be 16,777,216 each, **25,165,824** more parameters per layer.

**What this means:** the head count sets the query and output projections, while the key-value head
count sets the smaller key and value projections; grouped-query attention saves parameters there.

## What To Watch

- **num_key_value_heads.** Equal to the head count means multi-head attention, 1 means multi-query,
  anything between means grouped-query (Transformers' description of the key).
- **head_dim.** If it is set in the configuration, heads × head_dim may differ from hidden_size.
- **The KV cache.** It grows with key-value heads, not query heads; see the head dimension page.
