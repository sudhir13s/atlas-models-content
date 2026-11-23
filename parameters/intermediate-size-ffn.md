---
id: parameter/intermediate-size-ffn
type: parameter
slug: intermediate-size-ffn
schema_version: 3.1
title: Intermediate Size (FFN Width)
name: intermediate_size
priority: 3
importance: critical
applies_to:
- llm
- transformer
- moe
range: 2,048 — 53,248
controls: 'How wide the feed-forward block inside every layer is: each token''s vector is widened to this size and narrowed back.'
effects:
  decrease:
  - 'Fewer parameters in every layer, in a straight line: 3 × hidden_size fewer per unit in a gated FFN'
  - Less computation per token in the FFN
  - In the original Transformer's tests, d_ff 1024 scored 25.4 BLEU against 25.8 at 2048
  increase:
  - More parameters in every layer, in a straight line
  - More computation per token in the FFN
  - In the same tests, d_ff 4096 scored 26.2 BLEU, with 90 million parameters against 65 million
scale:
  min: 1024
  max: 65536
  typical_min: 2048
  typical_max: 53248
  spacing: log
typical_values:
- setting: Original Transformer, base model, d_model 512 (Vaswani et al.)
  value: '2048'
  source: https://arxiv.org/abs/1706.03762
- setting: BERT-base, hidden_size 768, two matrices (config.json)
  value: '3072'
  source: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
- setting: Qwen3-8B, hidden_size 4096, gated (config.json)
  value: '12288'
  source: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
- setting: Llama 3 8B, model dimension 4096, SwiGLU (Llama 3 paper, Table 3)
  value: '14336'
  source: https://arxiv.org/abs/2407.21783
- setting: Qwen3-32B, hidden_size 5120, gated (config.json)
  value: '25600'
  source: https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json
- setting: Llama 3 70B, model dimension 8192 (Llama 3 paper, Table 3)
  value: '28672'
  source: https://arxiv.org/abs/2407.21783
- setting: Llama 3 405B, model dimension 16384 (Llama 3 paper, Table 3)
  value: '53248'
  source: https://arxiv.org/abs/2407.21783
mistakes:
- mistake: Comparing FFN sizes without counting the matrices
  why: A plain FFN has two matrices; a gated one such as SwiGLU has three. Shazeer, who introduced these variants, cut d_ff by a factor of **2/3** (from 3072 to 2048) to keep the parameters and computation equal to the two-matrix version.
  fix: Multiply intermediate_size by the number of FFN matrices before comparing.
- mistake: Assuming the FFN is the same share of every model
  why: The share depends on the configuration. Computed from their config.json files, the FFN holds **66.4%** of Qwen3-8B's parameters but **44.3%** of Qwen3-0.6B's, where the embedding table takes 26.1%.
  fix: Compute the share for the model at hand, as in the figure.
- mistake: Reading intermediate_size as the size of a mixture-of-experts layer
  why: In a mixture-of-experts model each expert has its own FFN, with its own key. Qwen3-30B-A3B's config.json lists intermediate_size **6144** but moe_intermediate_size **768** for each of its **128** experts, 8 of them used per token.
  fix: See the MoE expert FFN size page for those models.
related:
- parameter/hidden-size
- parameter/activation-function
- parameter/moe-expert-ffn-size
- parameter/num-hidden-layers
visual:
  kind: count_breakdown
  models:
  - name: Qwen3-8B
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
  caption: Where the 8,190,735,360 parameters of Qwen3-8B sit, computed from its config.json part by part as Transformers builds the model; the total matches the published count.
tags:
- llm
- transformer
- ffn
- mlp
- swiglu
- parameters
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.95
- url: https://arxiv.org/abs/2002.05202
  title: GLU Variants Improve Transformer (Shazeer, 2020)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
  title: Qwen3-8B configuration (config.json)
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'Hugging Face Transformers source: modeling_qwen3.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bert/modeling_bert.py
  title: 'Hugging Face Transformers source: modeling_bert.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py
  title: 'Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)'
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-0.6B/blob/main/config.json
  title: Qwen3-0.6B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json
  title: Qwen3-32B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
  title: BERT-base uncased configuration (config.json)
  trust: 0.95
- url: https://arxiv.org/abs/2407.21783
  title: The Llama 3 Herd of Models (Llama Team, 2024)
  trust: 0.9
- url: https://arxiv.org/abs/2505.09388
  title: Qwen3 Technical Report (Qwen Team, 2025)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-30B-A3B/blob/main/config.json
  title: Qwen3-30B-A3B configuration (config.json)
  trust: 0.95
---

## Definition

**intermediate_size is the width the feed-forward block widens each token's vector to.**
Transformers describes the key as the "dimension of the MLP representations"; papers write
**d_ff**.

- **The feed-forward network (FFN)** is the second half of every layer. It works on each token on
  its own, unlike attention, which mixes tokens.
- **It widens** a vector of hidden_size numbers to intermediate_size, applies a non-linear function,
  and **narrows** it back.
- **It is large:** in Qwen3-8B it is three times hidden_size, in every one of 36 layers.
- **The figure** shows the result: the feed-forward blocks hold **66.4%** of Qwen3-8B's parameters.

**Analogy:** a workbench in every room of a factory, much wider than the corridor that brings the
parts in. The analogy shows the idea only; the exact counts are under Calculation.

## How It Works

**Widen, apply a function, narrow.**

1. **Plain FFN** (the original Transformer, BERT): one matrix widens to d_ff, a function such as
   ReLU or GELU is applied, a second matrix narrows back. Vaswani et al. use d_ff **2048** on
   d_model 512.
2. **Gated FFN** (SwiGLU in Llama 3 and Qwen3): two matrices widen in parallel, one result passes
   through the function and multiplies the other, then a third matrix narrows back.
3. **In Transformers' Qwen3 code** these are gate_proj and up_proj, hidden_size → intermediate_size,
   and down_proj, intermediate_size → hidden_size, with no biases.
4. **Shazeer's rule:** a gated FFN with d_ff two thirds of a plain one's has about the same
   parameters and computation.

## Calculation

**The FFN parameters of one layer.**

In words: hidden_size times intermediate_size, once per matrix.

$$
\text{FFN} = m \times d \times f \quad (+\ \text{biases in BERT})
$$

| Symbol | Meaning |
| --- | --- |
| $m$ | FFN matrices: 3 gated, 2 plain |
| $d$ | hidden_size |
| $f$ | intermediate_size |

BERT's two matrices also have biases, f + d numbers. For whole-model counts, see the
[parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**The FFN of Qwen3-8B, from its config.json** (the numbers on its catalog page): hidden_size
**4096**, intermediate_size **12288**, three matrices, 36 layers.

1. **Ratio:** 12288 ÷ 4096 = **3**.
2. **One layer:** 3 × 4096 × 12288 = **150,994,944**.
3. **Share of the layer:** 150,994,944 ÷ 192,946,432 ≈ **78.3%** (the layer total is on the hidden
   size page).
4. **All layers:** 36 × 150,994,944 = **5,435,817,984**.
5. **Share of the model:** 5,435,817,984 ÷ 8,190,735,360 ≈ **66.4%**.
6. **For comparison, BERT-base:** 2 × 768 × 3072 = **4,718,592** weights per layer, plus 3,840
   biases; ratio 3072 ÷ 768 = **4**.

**What this means:** most of a dense model's parameters sit in the FFN, so intermediate_size and
the number of FFN matrices together set most of its size.

## What To Watch

- **The activation.** A gated activation (SwiGLU, GeGLU) means three matrices; see the activation
  function page.
- **Mixture-of-experts.** The expert FFN size is a different key in those models.
- **The ratio to hidden_size.** It is a design choice: 3 in Qwen3-8B, 3.5 in Llama 3 8B, 4 in
  BERT-base.
