---
id: parameter/hidden-size
type: parameter
slug: hidden-size
schema_version: 3.1
title: Hidden Size (d_model, Width)
name: hidden_size
priority: 2
importance: critical
applies_to:
- llm
- transformer
- moe
range: 512 — 16,384
controls: 'The length of the vector that stands for each token inside the model: every layer reads and writes vectors of this size.'
effects:
  decrease:
  - 'Fewer parameters in every layer: about a quarter at half the width, when the FFN size and head counts shrink with it'
  - A smaller embedding table, vocab_size × hidden_size
  - In the original Transformer's tests, d_model 256 scored 24.5 BLEU against 25.8 at 512
  increase:
  - 'More parameters in every layer: about four times at double the width, when the FFN size and head counts grow with it'
  - A larger embedding table and output head
  - In the original Transformer's tests, d_model 1024 scored 26.0 BLEU, with 168 million parameters against 65 million
scale:
  min: 256
  max: 32768
  typical_min: 512
  typical_max: 16384
  spacing: log
typical_values:
- setting: Original Transformer, base model (Vaswani et al.)
  value: '512'
  source: https://arxiv.org/abs/1706.03762
- setting: BERT-base (config.json)
  value: '768'
  source: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
- setting: Qwen3-0.6B (config.json)
  value: '1024'
  source: https://huggingface.co/Qwen/Qwen3-0.6B/blob/main/config.json
- setting: Qwen3-8B (config.json)
  value: '4096'
  source: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
- setting: Qwen3-32B (config.json)
  value: '5120'
  source: https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json
- setting: Llama 3 70B (Llama 3 paper, Table 3)
  value: '8192'
  source: https://arxiv.org/abs/2407.21783
- setting: Llama 3 405B (Llama 3 paper, Table 3)
  value: '16384'
  source: https://arxiv.org/abs/2407.21783
mistakes:
- mistake: Assuming hidden_size always equals heads × head size
  why: Transformers builds the query projection as hidden_size × (num_attention_heads × head_dim), and `head_dim` can be set on its own. **Qwen3-32B** has hidden_size **5120** but 64 heads of 128, a query width of **8192**; Qwen3-0.6B has 1024 against 16 × 128 = 2048.
  fix: Read `head_dim` from the configuration instead of dividing hidden_size by the heads.
- mistake: Expecting the parameter count to double when hidden_size doubles
  why: 'Every weight matrix inside a layer has hidden_size on one side and, in models such as Qwen3, a size that grows with it on the other. Doubling all of them multiplies a layer''s count by about **four**: one Qwen3-8B layer holds 192,946,432 parameters, the same layer at half the width 48,238,848.'
  fix: Compute the count from the configuration, as in the worked example.
- mistake: Thinking width or depth alone decides quality
  why: 'Kaplan et al. found language-model loss depends **very weakly** on shape when the number of non-embedding parameters is fixed: a model of 6 layers and width 4288 came within 3% of the loss of one with 48 layers and width 1600.'
  fix: Compare models by their parameter count first, then by shape.
related:
- parameter/num-attention-heads
- parameter/head-dim
- parameter/intermediate-size-ffn
- parameter/num-hidden-layers
- parameter/vocab-size
visual:
  kind: count_sweep
  model:
    name: Qwen3-8B
    vocabulary_size: 151936
    hidden_size: 4096
    intermediate_size: 12288
    num_layers: 36
    attention_heads: 32
    kv_heads: 8
    head_size: 128
    tie_word_embeddings: false
    ffn_matrices: 3
    qk_norm: true
  vary: width
  values:
  - 1024
  - 2048
  - 3072
  - 4096
  - 5120
  - 6144
  - 8192
  measure: per_layer
  caption: The parameters in one layer of Qwen3-8B, computed from its config.json, as hidden_size changes with intermediate_size and both head counts scaled by the same factor and head_dim kept at 128. The model's own width, 4096, is marked; the other widths are not real Qwen3 models.
tags:
- llm
- transformer
- width
- d-model
- structure
- parameters
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.95
- url: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
  title: Qwen3-8B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/Qwen/Qwen3-8B
  title: Qwen3-8B model card
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'Hugging Face Transformers source: modeling_qwen3.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py
  title: 'Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)'
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json
  title: Qwen3-32B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/Qwen/Qwen3-0.6B/blob/main/config.json
  title: Qwen3-0.6B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
  title: BERT-base uncased configuration (config.json)
  trust: 0.95
- url: https://arxiv.org/abs/2407.21783
  title: The Llama 3 Herd of Models (Llama Team, 2024)
  trust: 0.9
- url: https://arxiv.org/abs/2001.08361
  title: Scaling Laws for Neural Language Models (Kaplan et al., 2020)
  trust: 0.9
---

## Definition

**hidden_size is how many numbers stand for each token as it passes through the model.**
Papers often write it as **d_model** or **d**; it is also called the model's **width**.

- **A token** is a piece of text, a word or part of one. The model turns each into a list of
  numbers, a **vector**.
- **hidden_size** is that vector's length. Transformers describes the key as the "dimension of the
  hidden representations".
- **Every layer** reads vectors of this length and writes vectors of the same length, so it sets
  the size of almost every weight matrix.
- **The figure** shows what that costs: one layer of Qwen3-8B holds about **193 million**
  parameters at width 4096, and about four times as many at double the width.

**Analogy:** the width of a conveyor belt that carries each token from layer to layer. A wider belt
carries more per token, and every machine along it must be built wider too. The analogy shows the
idea only; the exact counts are under Calculation.

## How It Works

**Each layer turns hidden_size numbers into hidden_size numbers, through matrices sized by it.**

1. **Embedding.** Each token's ID picks one row of the embedding table, a vector of hidden_size
   numbers.
2. **Attention.** The query, key and value projections each take hidden_size numbers in; the output
   projection gives hidden_size numbers back.
3. **Feed-forward.** The FFN widens the vector from hidden_size to intermediate_size and narrows it
   back.
4. **Residual stream.** Each sub-layer's output is added to its input, so the vector stays
   hidden_size long from the first layer to the last.
5. **Output head.** The last vector is multiplied by a hidden_size × vocab_size matrix to score
   every possible next token.

In the original Transformer (Vaswani et al.) d_model is **512**, and the embeddings and every
sub-layer output have that size.

## Calculation

**How hidden_size enters the parameter count of one layer** (as Transformers builds Qwen3).

In words: each attention projection is hidden_size times a width set by the heads; each FFN matrix
is hidden_size times intermediate_size; each norm has hidden_size weights.

$$
\text{attention} = 2\,d\,(n_h h) + 2\,d\,(n_{kv} h) \qquad \text{FFN} = 3\,d\,f \qquad \text{norms} = 2\,d
$$

| Symbol | Meaning |
| --- | --- |
| $d$ | hidden_size |
| $n_h$, $n_{kv}$ | num_attention_heads, num_key_value_heads |
| $h$ | head_dim |
| $f$ | intermediate_size |

- **When $f$, $n_h$ and $n_{kv}$ grow with $d$,** every term above grows with $d^2$, which is why
  the figure's curve bends upward.
- **The embedding table** adds vocab_size × $d$, and an untied output head the same again.
- **For the whole model** and other architectures, see the
  [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**One layer of Qwen3-8B, from its config.json** (the numbers on the catalog page for Qwen3-8B):
hidden_size **4096**, 32 query heads and 8 key-value heads of head_dim **128**, intermediate_size
**12288**, a gated FFN of three matrices, and Qwen3's two per-head norms.

1. **Query and output projections:** 4096 × (32 × 128) = 4096 × 4096 = **16,777,216** each.
2. **Key and value projections:** 4096 × (8 × 128) = 4096 × 1024 = **4,194,304** each.
3. **Attention:** 2 × 16,777,216 + 2 × 4,194,304 = **41,943,040**.
4. **Feed-forward:** 3 × 4096 × 12288 = **150,994,944**.
5. **Norms:** 2 × 4096 + 2 × 128 = **8,448**.
6. **One layer:** 41,943,040 + 150,994,944 + 8,448 = **192,946,432**.
7. **At half the width** (2048, with 6144, 16 and 4 heads): **48,238,848**, which is 192,946,432 ÷
   48,238,848 ≈ **4.0** times fewer.

**What this means:** width enters a layer twice over, so halving it cuts a layer to about a quarter.
The whole model is checked on the number of layers page: these counts add up exactly to Qwen3-8B's
published total.

## What To Watch

- **head_dim in the configuration.** When it is set, the query width is heads × head_dim, which
  need not equal hidden_size.
- **The FFN size.** Its ratio to hidden_size differs by design: 3 in Qwen3-8B, 4 in BERT-base.
- **Embedding share.** In small models the vocab_size × hidden_size table is a large part of the
  total; see the vocabulary size page.
