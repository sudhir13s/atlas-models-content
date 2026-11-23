---
id: parameter/num-hidden-layers
type: parameter
slug: num-hidden-layers
schema_version: 3.1
title: Number of Hidden Layers (Depth)
name: num_hidden_layers
priority: 1
importance: critical
applies_to:
- llm
- transformer
- moe
range: 6 — 126
controls: 'How many transformer blocks are stacked one after another: the model''s depth.'
effects:
  decrease:
  - 'Fewer parameters: the per-layer count is removed once for every layer taken away'
  - Less computation per token, which passes through fewer blocks
  - In the original Transformer's tests, 2 layers scored 23.7 BLEU against 25.8 for 6
  increase:
  - 'More parameters, in a straight line: each layer adds the same count'
  - More computation per token
  - In the original Transformer's tests, 8 layers scored 25.5 BLEU with 80 million parameters, against 25.8 for 6 with 65 million
scale:
  min: 1
  max: 256
  typical_min: 6
  typical_max: 126
  spacing: log
typical_values:
- setting: Original Transformer, encoder and decoder each (Vaswani et al.)
  value: '6'
  source: https://arxiv.org/abs/1706.03762
- setting: BERT-base (config.json)
  value: '12'
  source: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
- setting: Qwen3-0.6B (config.json)
  value: '28'
  source: https://huggingface.co/Qwen/Qwen3-0.6B/blob/main/config.json
- setting: Llama 3 8B (Llama 3 paper, Table 3)
  value: '32'
  source: https://arxiv.org/abs/2407.21783
- setting: Qwen3-8B (config.json)
  value: '36'
  source: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
- setting: Qwen3-32B (config.json)
  value: '64'
  source: https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json
- setting: Llama 3 70B (Llama 3 paper, Table 3)
  value: '80'
  source: https://arxiv.org/abs/2407.21783
- setting: Llama 3 405B (Llama 3 paper, Table 3)
  value: '126'
  source: https://arxiv.org/abs/2407.21783
mistakes:
- mistake: Expecting half the layers to give half the parameters
  why: The embedding table and the output head do not depend on depth. Qwen3-8B has **8,190,735,360** parameters with 36 layers; with 18 layers the count is **4,717,699,584**, 58% of it, because 1,244,659,712 of them sit in the embedding and the head.
  fix: Subtract the embedding and head first; only the rest scales with the number of layers.
- mistake: Reading depth as the measure of a model's ability
  why: Kaplan et al. found loss depends **very weakly** on depth when the number of non-embedding parameters is fixed; only models with fewer than 2 layers or extreme depth-to-width ratios left the common trend.
  fix: Compare total or non-embedding parameter counts before comparing depth.
- mistake: Counting the original Transformer as 6 layers in all
  why: Vaswani et al. use **6 layers in the encoder and 6 in the decoder**. A decoder-only model such as Qwen3 has one stack, and num_hidden_layers counts its blocks.
  fix: Check whether a model has one stack or two before comparing layer counts.
related:
- parameter/hidden-size
- parameter/intermediate-size-ffn
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
  vary: num_layers
  values:
  - 12
  - 24
  - 36
  - 48
  - 64
  measure: total
  caption: The total parameters of Qwen3-8B, computed from its config.json, if only num_hidden_layers changed. The model's own 36 layers are marked and give its published total; the other depths are not real Qwen3 models.
tags:
- llm
- transformer
- depth
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

**num_hidden_layers is how many identical blocks the model stacks: its depth.**
Transformers describes the key as the "number of hidden layers in the Transformer decoder".

- **A layer** (or block) is one attention sub-layer followed by one feed-forward sub-layer, each
  with a norm and a residual connection.
- **Every token** passes through all of them, in order.
- **Each layer has the same shape,** so it adds the same number of parameters; only the embedding
  table and the output head stand outside the stack.
- **The figure** shows that: Qwen3-8B's total rises in a straight line with depth, by about
  **193 million** per layer.

**Analogy:** floors of a building that all share one floor plan. Each floor adds the same area; the
foundation and the roof, here the embedding and the output head, are built once. The analogy shows
the idea only; the exact counts are under Calculation.

## How It Works

**The output of one layer is the input of the next.**

1. **Embedding.** Each token becomes a vector of hidden_size numbers.
2. **Layer 1.** Attention mixes information between tokens; the FFN transforms each token's
   vector; both add their result to the vector they received.
3. **Layers 2 to L.** The same steps repeat with each layer's own weights.
4. **Final norm and output head.** The last vector is normalised and scored against the
   vocabulary.

Vaswani et al. used **6** layers in the encoder and **6** in the decoder; the Llama 3 paper lists
**32, 80 and 126** layers for its 8B, 70B and 405B models.

## Calculation

**How num_hidden_layers enters the parameter count.**

In words: the parameters of one layer, times the number of layers, plus the embedding table, the
output head (unless tied) and the final norm.

$$
\text{total} = L \times \text{one layer} + V d + V d + d
$$

| Symbol | Meaning |
| --- | --- |
| $L$ | num_hidden_layers |
| one layer | attention + feed-forward + norms; see the hidden size page |
| $V$ | vocab_size |
| $d$ | hidden_size |

For other architectures and live counts, see the
[parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**The whole of Qwen3-8B, from its config.json** (the numbers on its catalog page): 36 layers,
hidden_size 4096, vocab_size 151936, an untied output head, and **192,946,432** parameters per layer
(worked out on the hidden size page).

1. **All layers:** 36 × 192,946,432 = **6,946,071,552**.
2. **Embedding table:** 151,936 × 4096 = **622,329,856**.
3. **Output head,** not tied to the embedding: another **622,329,856**.
4. **Final norm:** **4,096**.
5. **Total:** 6,946,071,552 + 622,329,856 + 622,329,856 + 4,096 = **8,190,735,360**, exactly the
   total on the catalog page.
6. **Check against the card:** without the embedding and the head the total is **6,946,075,648**;
   the card lists 6.95B non-embedding parameters.
7. **With 18 layers:** 18 × 192,946,432 + 1,244,663,808 = **4,717,699,584**.

**What this means:** depth multiplies the layers and nothing else. Halving it here removes 42% of
the parameters, not half, because 15% of the model sits in the embedding and the head.

## What To Watch

- **One stack or two.** An encoder-decoder model has two counts; a decoder-only model one.
- **The embedding and head.** In small models they are a large share, so depth changes the total
  less than the layer count suggests.
- **Which count is quoted.** Kaplan et al. and many model cards quote parameters without the
  embedding; Qwen3-8B's card gives both, 8.2B and 6.95B.
