---
id: parameter/positional-encoding
type: parameter
slug: positional-encoding
schema_version: 3.1
title: Positional Encoding (RoPE / Learned / ALiBi)
name: rope_theta
priority: 11
importance: optional
applies_to:
- llm
- transformer
- moe
range: 10,000 — 1,000,000
controls: How the model knows where each token sits; in rotary models rope_theta, the base, sets how fast each pair of numbers in a head turns per position.
effects:
  decrease:
  - Every pair after the first turns faster per position
  - 'The slowest pair repeats sooner: every 54,410 positions with base 10,000 and head_dim 128'
  - 'No change in the parameter count: rotary positions store none'
  increase:
  - Every pair after the first turns more slowly; the first always turns 1 radian per position
  - 'The slowest pair repeats later: every 5,063,256 positions with base 1,000,000'
  - The Qwen3 report raised the base from 10,000 to 1,000,000 for its long-context stage
scale:
  min: 1000
  max: 10000000
  typical_min: 10000
  typical_max: 1000000
  spacing: log
typical_values:
- setting: RoFormer, the base of θᵢ = 10000^(−2(i−1)/d) (Su et al.)
  value: '10000'
  source: https://arxiv.org/abs/2104.09864
- setting: Mistral-7B-v0.1 (config.json)
  value: '10000'
  source: https://huggingface.co/mistralai/Mistral-7B-v0.1/blob/main/config.json
- setting: DeepSeek-V3, with YaRN scaling (config.json)
  value: '10000'
  source: https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json
- setting: SmolLM2-135M (config.json)
  value: '100000'
  source: https://huggingface.co/HuggingFaceTB/SmolLM2-135M/blob/main/config.json
- setting: Mixtral-8x7B (config.json)
  value: '1000000'
  source: https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json
- setting: Qwen3-8B (config.json)
  value: '1000000'
  source: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
mistakes:
- mistake: Thinking rotary positions are added to the token vectors
  why: 'RoFormer calls its method multiplicative, unlike the added position vectors of earlier models: queries and keys are rotated. Transformers'' Qwen3 code rotates only the queries and keys, not the values or the token vectors.'
  fix: Look for positions inside attention in rotary models, not in the embeddings.
- mistake: Expecting the neighbouring-number pairing of the paper in Transformers' code
  why: RoFormer rotates numbers 1 and 2, 3 and 4, and so on. Transformers' rotate_half pairs number j with number j + head_dim ÷ 2, using the same set of angles.
  fix: Match the pairing to the code a checkpoint was trained with.
- mistake: Assuming the encoding choice decides quality on its own
  why: 'Vaswani et al. tried learned position embeddings in place of their sinusoids and found nearly identical results: **25.7** against **25.8** BLEU. Press et al. show that how far a model can run past its training length depends on the method; they introduced ALiBi for that.'
  fix: Judge a positional scheme by the lengths it was trained and tested on.
related:
- parameter/context-length
- parameter/head-dim
- parameter/token-embedding
visual:
  kind: rope_rotation
  model: Qwen3-8B
  rope_theta: 1000000
  head_size: 128
  pair: 0
  query:
  - 1
  - 0
  key:
  - 0.6
  - 0.8
  positions:
  - query: 0
    key: 3
  - query: 10
    key: 13
  - query: 2
    key: 4
  caption: 'The first pair of numbers of a Qwen3-8B head (rope_theta 1,000,000 and head_dim 128 from its config.json) turns 1 radian per position. The query and key are an example, not from a trained model: at the same distance, 3, the score is the same at any position; at distance 2 it differs.'
tags:
- llm
- transformer
- positional-encoding
- rope
- alibi
- parameters
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/2104.09864
  title: 'RoFormer: Enhanced Transformer with Rotary Position Embedding (Su et al., 2021)'
  trust: 0.9
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.95
- url: https://arxiv.org/abs/2108.12409
  title: 'Train Short, Test Long: Attention with Linear Biases Enables Input Length Extrapolation (Press et al., 2021)'
  trust: 0.85
- url: https://arxiv.org/abs/2505.09388
  title: Qwen3 Technical Report (Qwen Team, 2025)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
  title: Qwen3-8B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/mistralai/Mistral-7B-v0.1/blob/main/config.json
  title: Mistral-7B-v0.1 configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json
  title: Mixtral-8x7B-Instruct-v0.1 configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json
  title: DeepSeek-V3 configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/HuggingFaceTB/SmolLM2-135M/blob/main/config.json
  title: SmolLM2-135M configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
  title: BERT-base uncased configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/openai-community/gpt2/blob/main/config.json
  title: GPT-2 configuration (config.json)
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'Hugging Face Transformers source: modeling_qwen3.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bert/modeling_bert.py
  title: 'Hugging Face Transformers source: modeling_bert.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gpt2/modeling_gpt2.py
  title: 'Hugging Face Transformers source: modeling_gpt2.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bloom/modeling_bloom.py
  title: 'Hugging Face Transformers source: modeling_bloom.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py
  title: 'Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)'
  trust: 0.9
---

## Definition

**A positional encoding tells the model where each token sits in the text.** Vaswani et al. note
that a model without recurrence or convolution must be given the order of the tokens.

- **Learned positions** (BERT, GPT-2): a trained table with one vector per position, added to the
  token vectors.
- **Sinusoids** (the original Transformer): fixed sine and cosine waves, also added.
- **Rotary positions, RoPE** (RoFormer; Qwen3, Mistral, Mixtral, DeepSeek-V3): queries and keys are
  rotated by an angle that grows with position.
- **ALiBi** (Press et al.; BLOOM in Transformers): no position vectors; attention scores get a
  penalty that grows with distance.
- **rope_theta** is the configuration key of rotary models: the base of the angles. Transformers
  describes it as part of the RoPE parameters.
- **The figure** turns an example query and key by Qwen3-8B's first rotary angle.

**Analogy:** clock hands. Each pair of numbers is a hand turning at its own speed, and two tokens
compare the angle between their hands. The analogy shows the idea only.

## How It Works

**Split the head into pairs, turn each pair by position × its angle.**

1. **Pairs.** A head of head_dim numbers has head_dim ÷ 2 pairs; Qwen3-8B has **64**.
2. **Angles.** Pair i turns by θᵢ = rope_theta^(−2i ÷ head_dim) radians per position, counting i
   from 0, as Transformers computes it (RoFormer writes the same with i from 1).
3. **Turn.** At position m, the query and key pairs are rotated by m × θᵢ.
4. **Compare.** RoFormer shows the score then depends only on the distance between the positions,
   and decays as the distance grows.

**The other schemes in numbers:** BERT's position table is 512 × 768 = **393,216** parameters;
GPT-2's is 1024 × 768 = **786,432**; rotary positions and ALiBi store none.

## Calculation

**The angle of each pair and how long it takes to come round.**

In words: the first pair turns one radian per position; each later pair turns more slowly, by a
factor set by rope_theta.

$$
\theta_i = b^{-2i/h} \qquad \text{positions per full turn} = 2\pi \div \theta_i
$$

| Symbol | Meaning |
| --- | --- |
| $b$ | rope_theta |
| $h$ | head_dim |
| $i$ | the pair, 0 to h ÷ 2 − 1 |

Learned positions cost max_position_embeddings × hidden_size parameters; rotary positions cost
none. For whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Qwen3-8B, from its config.json** (the numbers on its catalog page): rope_theta **1,000,000**,
head_dim **128**, so 64 pairs.

1. **Pair 0:** 1,000,000^0 = **1** radian per position, **57.2958°**.
2. **Pair 1:** 1,000,000^(−2/128) ≈ **0.8058** radian per position.
3. **Pair 63, the slowest:** 1,000,000^(−126/128) ≈ **1.2409 × 10⁻⁶** radian; a full turn takes
   2π ÷ 1.2409 × 10⁻⁶ ≈ **5,063,256** positions.
4. **The figure's example** (made-up numbers): q = (1, 0), k = (0.6, 0.8), unturned score
   **0.6**.
5. **Query at 0, key at 3:** k turns by 171.887°, score **−0.707**; at 10 and 13, also
   **−0.707**; at 2 and 4, **−0.977**.

**What this means:** the score depends on the distance, not the place; rope_theta sets how slowly
the later pairs turn, and with base 10,000 the slowest pair would come round after **54,410**
positions.

## What To Watch

- **rope_scaling.** Context extension such as YaRN changes the angles; DeepSeek-V3 ships with it,
  Qwen3-8B without.
- **The pairing.** Transformers pairs number j with j + head_dim ÷ 2.
- **max_position_embeddings.** With learned positions it is the table's length; see the context
  length page.
