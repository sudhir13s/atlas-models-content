---
id: parameter/token-embedding
type: parameter
slug: token-embedding
schema_version: 3.1
title: Token Embedding Table
name: embed_tokens
priority: 6
importance: critical
applies_to:
- llm
- transformer
- moe
range: 23,440,896 — 926,679,040 parameters
controls: 'The table that turns each token number into the model''s first vector for it: vocab_size rows of hidden_size numbers.'
effects:
  decrease:
  - Fewer parameters, in a straight line with vocab_size and with hidden_size
  - 'The same work per token: one row is read, whatever the table''s size'
  - A smaller share of the model; in Qwen3-32B the table is 2.4% of all parameters
  increase:
  - More parameters, in a straight line
  - Still one row read per token
  - 'A larger share in a small model: 21.0% of SmolLM2-135M'
scale:
  min: 5000000
  max: 3000000000
  typical_min: 23440896
  typical_max: 926679040
  spacing: log
typical_values:
- setting: BERT-base uncased, 30,522 × 768 (config.json)
  value: '23440896'
  source: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
- setting: SmolLM2-135M, 49,152 × 576 (config.json)
  value: '28311552'
  source: https://huggingface.co/HuggingFaceTB/SmolLM2-135M/blob/main/config.json
- setting: Mistral-7B-v0.1, 32,000 × 4,096 (config.json)
  value: '131072000'
  source: https://huggingface.co/mistralai/Mistral-7B-v0.1/blob/main/config.json
- setting: Qwen3-8B, 151,936 × 4,096 (config.json)
  value: '622329856'
  source: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
- setting: Qwen3-32B, 151,936 × 5,120 (config.json)
  value: '777912320'
  source: https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json
- setting: DeepSeek-V3, 129,280 × 7,168 (config.json)
  value: '926679040'
  source: https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json
mistakes:
- mistake: Assuming every model scales its embeddings by √hidden_size
  why: Vaswani et al. multiply the embedding weights by **√d_model**. Transformers' Qwen3 code passes the looked-up rows to the first layer unchanged, with no such factor.
  fix: Check the model's own code before assuming a scale on the embeddings.
- mistake: Taking the token table as the whole input
  why: In BERT it is one of three tables. Transformers' BERT code adds a word embedding, a position embedding (**512** rows) and a token-type embedding (**2** rows), then applies a LayerNorm. Qwen3 has only the token table; positions enter later, inside attention.
  fix: Read the model's embedding code to see what is added to the token rows.
- mistake: Reading vocab_size as the tokenizer's vocabulary
  why: The table can have spare rows. The Qwen3 report gives a vocabulary of **151,669** tokens, while Qwen3's config.json sets vocab_size **151,936**; the table has **267** rows the tokenizer does not produce.
  fix: Size the table from config.json; take the tokenizer's vocabulary from the tokenizer.
related:
- parameter/vocab-size
- parameter/hidden-size
- parameter/tied-embeddings
- parameter/output-head
visual:
  kind: count_breakdown
  models:
  - name: SmolLM2-135M
    vocabulary_size: 49152
    hidden_size: 576
    intermediate_size: 1536
    num_layers: 30
    attention_heads: 9
    kv_heads: 3
    head_size: 64
    tie_word_embeddings: true
    ffn_matrices: 3
    qk_norm: false
  - name: Qwen3-32B
    vocabulary_size: 151936
    hidden_size: 5120
    intermediate_size: 25600
    num_layers: 64
    attention_heads: 64
    kv_heads: 8
    head_size: 128
    tie_word_embeddings: false
    ffn_matrices: 3
    qk_norm: true
  caption: 'Where the parameters of a small and a large model sit, computed from their config.json files: the token table is 21.0% of SmolLM2-135M but 2.4% of Qwen3-32B. SmolLM2-135M sets no head_dim; Transformers'' Llama configuration then uses hidden_size ÷ heads, 576 ÷ 9 = 64. Both totals match the counts on their catalog pages.'
tags:
- llm
- transformer
- embedding
- input
- parameters
updated: '2026-11-23'
sources:
- url: https://github.com/pytorch/pytorch/blob/main/torch/nn/modules/sparse.py
  title: 'PyTorch source: torch.nn.Embedding (sparse.py)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'Hugging Face Transformers source: modeling_qwen3.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bert/modeling_bert.py
  title: 'Hugging Face Transformers source: modeling_bert.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/configuration_llama.py
  title: 'Hugging Face Transformers source: configuration_llama.py'
  trust: 0.9
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: Qwen3 Technical Report (Qwen Team, 2025)
  trust: 0.9
- url: https://huggingface.co/HuggingFaceTB/SmolLM2-135M/blob/main/config.json
  title: SmolLM2-135M configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json
  title: Qwen3-32B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
  title: Qwen3-8B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
  title: BERT-base uncased configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/mistralai/Mistral-7B-v0.1/blob/main/config.json
  title: Mistral-7B-v0.1 configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json
  title: DeepSeek-V3 configuration (config.json)
  trust: 0.95
---

## Definition

**The token embedding table turns each token number into the model's first vector for that
token.** In Transformers' Qwen3 code it is the module embed_tokens, a PyTorch embedding of
vocab_size rows and hidden_size columns; it is a module name, not a configuration key.

- **A token** is a piece of text; the tokenizer gives each one a number from 0 to vocab_size − 1.
- **A row** of the table is that token's vector: hidden_size numbers, learned in training.
- **A lookup, not a multiplication:** PyTorch describes its embedding as a simple lookup table that
  returns the stored vector for each index.
- **The figure** shows its size against the rest: **21.0%** of SmolLM2-135M, **2.4%** of Qwen3-32B.

**Analogy:** a locker room, where each token number opens one locker holding that token's vector.
The analogy shows the idea only; the exact sizes are under Calculation.

## How It Works

**Number in, row out, then into the layers.**

1. **Tokenize.** The text becomes token numbers.
2. **Look up.** Each number selects its row; a prompt of n tokens becomes n vectors of hidden_size
   numbers.
3. **Pass on.** In Qwen3 these vectors go straight into the first layer. Positions are added later,
   by rotating queries and keys inside attention.
4. **Reuse at the end, if tied.** With tie_word_embeddings true, the same table scores every token at
   the output; see the tied embeddings page.

## Calculation

**The table's parameters.**

In words: one row per token, one column per hidden number.

$$
\text{embedding} = V \times d
$$

| Symbol | Meaning |
| --- | --- |
| $V$ | vocab_size |
| $d$ | hidden_size |

- **Tied,** the output head reuses this table and adds nothing; **untied,** it adds another
  $V \times d$.
- For whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**SmolLM2-135M, from its config.json** (the numbers on its catalog page): vocab_size **49,152**,
hidden_size **576**, tied output head.

1. **Table size:** 49,152 × 576 = **28,311,552** parameters.
2. **One lookup:** a token numbered 1,000 (an example) reads row 1,000, which is **576** numbers.
3. **Whole model:** **134,515,008** parameters, computed layer by layer; the catalog page gives the
   same count.
4. **Share:** 28,311,552 ÷ 134,515,008 ≈ **21.0%**, and the tied head reuses the same table.
5. **For comparison, Qwen3-32B:** 151,936 × 5,120 = **777,912,320**, only ≈ **2.4%** of its
   32,762,123,264.

**What this means:** the table depends only on vocab_size and hidden_size, not on the number of
layers, so a small model with a large vocabulary spends a large share of itself on it.

## What To Watch

- **vocab_size and hidden_size.** Together they set the table; see their pages.
- **tie_word_embeddings.** Whether the table also serves as the output head.
- **Extra input tables.** Models such as BERT add position and token-type embeddings.
