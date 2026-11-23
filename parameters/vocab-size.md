---
id: parameter/vocab-size
type: parameter
slug: vocab-size
schema_version: 3.1
title: Vocabulary Size
name: vocab_size
priority: 4
importance: critical
applies_to:
- llm
- transformer
- moe
range: 30,522 — 256,128
controls: 'How many different tokens the model can read and write: the number of rows in its embedding table and of scores its output head gives.'
effects:
  decrease:
  - 'A smaller embedding table and output head: hidden_size fewer parameters per token removed, twice if untied'
  - Fewer scores to compute for each next token
  - Text splits into more, shorter tokens
  increase:
  - A larger embedding table and output head
  - More scores to compute for each next token
  - 'Text can split into fewer tokens: the Llama 3 team report 3.94 characters per token against 3.17 for Llama 2''s tokenizer on English'
scale:
  min: 10000
  max: 400000
  typical_min: 30522
  typical_max: 256128
  spacing: log
typical_values:
- setting: BERT-base (config.json); the BERT paper describes a 30,000-token WordPiece vocabulary
  value: '30522'
  source: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
- setting: GPT-2 (GPT-2 paper)
  value: '50257'
  source: https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf
- setting: Llama 3 (Llama 3 paper, Table 3)
  value: '128000'
  source: https://arxiv.org/abs/2407.21783
- setting: Qwen3-8B and Qwen3-0.6B (config.json)
  value: '151936'
  source: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
- setting: Qwen3 tokenizer (Qwen3 report)
  value: '151669'
  source: https://arxiv.org/abs/2505.09388
- setting: Gemma 2, 2B to 27B (Gemma 2 report, Table 1)
  value: '256128'
  source: https://arxiv.org/abs/2408.00118
mistakes:
- mistake: Reading vocab_size as the number of tokens the tokenizer has
  why: 'vocab_size is the number of rows of the embedding table. Qwen3''s config.json says **151,936**, while the Qwen3 report gives its tokenizer a vocabulary of **151,669**: the table has 267 more rows than there are tokens.'
  fix: Use vocab_size for parameter counts and the tokenizer for what text maps to.
- mistake: Counting the embedding once when the output head is untied
  why: An untied output head is a second vocab_size × hidden_size matrix. Qwen3-8B's config.json has tie_word_embeddings **false**, so its 622,329,856 embedding parameters appear twice; Qwen3-0.6B ties them, as its config.json and the Qwen3 report state.
  fix: Check tie_word_embeddings before adding up the model.
- mistake: Ignoring the vocabulary in small models
  why: Computed from its config.json, the embedding table holds **26.1%** of Qwen3-0.6B's parameters even though it is tied, against **7.6%** for the embedding table of Qwen3-8B. Kaplan et al. leave embedding parameters out of model size because the trends are cleaner without them.
  fix: Look at non-embedding parameters when comparing models of different vocabularies.
related:
- parameter/token-embedding
- parameter/tied-embeddings
- parameter/output-head
- parameter/hidden-size
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
  - name: Qwen3-0.6B
    vocabulary_size: 151936
    hidden_size: 1024
    intermediate_size: 3072
    num_layers: 28
    attention_heads: 16
    kv_heads: 8
    head_size: 128
    tie_word_embeddings: true
    ffn_matrices: 3
    qk_norm: true
  caption: 'The same vocabulary of 151,936 rows in two Qwen3 models, computed from their config.json files: 7.6% of Qwen3-8B, plus 7.6% again for its untied output head, but 26.1% of Qwen3-0.6B, whose head is tied to the embedding.'
tags:
- llm
- transformer
- tokenizer
- vocabulary
- embedding
- parameters
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
  title: Qwen3-8B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/Qwen/Qwen3-0.6B/blob/main/config.json
  title: Qwen3-0.6B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/Qwen/Qwen3-0.6B
  title: Qwen3-0.6B model card
  trust: 0.9
- url: https://arxiv.org/abs/2505.09388
  title: Qwen3 Technical Report (Qwen Team, 2025)
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'Hugging Face Transformers source: modeling_qwen3.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py
  title: 'Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)'
  trust: 0.9
- url: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
  title: BERT-base uncased configuration (config.json)
  trust: 0.95
- url: https://arxiv.org/abs/1810.04805
  title: 'BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)'
  trust: 0.9
- url: https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf
  title: Language Models are Unsupervised Multitask Learners (GPT-2, Radford et al., 2019)
  trust: 0.9
- url: https://arxiv.org/abs/2407.21783
  title: The Llama 3 Herd of Models (Llama Team, 2024)
  trust: 0.9
- url: https://arxiv.org/abs/2408.00118
  title: 'Gemma 2: Improving Open Language Models at a Practical Size (Gemma Team, 2024)'
  trust: 0.9
- url: https://arxiv.org/abs/2001.08361
  title: Scaling Laws for Neural Language Models (Kaplan et al., 2020)
  trust: 0.9
---

## Definition

**vocab_size is how many different tokens the model knows.**
Transformers describes the key as the number of different tokens that can be represented by the
input IDs.

- **A tokenizer** splits text into tokens, pieces from a fixed list, and gives each an ID.
- **The embedding table** has one row of hidden_size numbers per ID; vocab_size is its number of
  rows.
- **The output head** gives one score per ID for the next token, so it has vocab_size outputs; it
  is either a second table or the same one reused (**tied**).
- **The figure** shows the cost: the same 151,936 rows are **7.6%** of Qwen3-8B but **26.1%** of
  Qwen3-0.6B.

**Analogy:** the number of words in a phrasebook. A bigger book covers more phrases in one entry
each, and is heavier to carry. The analogy shows the idea only; the exact counts are under
Calculation.

## How It Works

**One row in, one score out, per token in the vocabulary.**

1. **Tokenize.** The text becomes a list of IDs from 0 to vocab_size − 1.
2. **Embed.** Each ID picks its row of the embedding table, a vector of hidden_size numbers.
3. **Run the layers.** The vectors pass through every layer.
4. **Score.** The output head multiplies the last vector by a hidden_size × vocab_size matrix: one
   score per possible next token.
5. **Tie or not.** With tie_word_embeddings true the head reuses the embedding table, as in
   Qwen3-0.6B; with false, as in Qwen3-8B, it is a separate matrix.

**A larger vocabulary can shorten text:** the Llama 3 team grew theirs to 128K tokens (100K from
tiktoken plus 28K for other languages) and report English text at 3.94 characters per token,
against 3.17 for the Llama 2 tokenizer.

## Calculation

**The parameters the vocabulary costs.**

In words: one row of hidden_size numbers per token for the embedding, and the same again for an
untied output head.

$$
\text{embedding} = V \times d \qquad \text{output head} = V \times d \ \ (0 \text{ if tied})
$$

| Symbol | Meaning |
| --- | --- |
| $V$ | vocab_size |
| $d$ | hidden_size |

For whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Qwen3-8B, from its config.json** (the numbers on its catalog page): vocab_size **151,936**,
hidden_size **4096**, tie_word_embeddings **false**, total 8,190,735,360.

1. **Embedding table:** 151,936 × 4096 = **622,329,856**.
2. **Output head,** untied: another **622,329,856**.
3. **Together:** **1,244,659,712**, which is 1,244,659,712 ÷ 8,190,735,360 ≈ **15.2%** of the model.
4. **Qwen3-0.6B,** hidden_size 1024, tied: 151,936 × 1024 = **155,582,464**, counted once.
5. **Its share:** 155,582,464 ÷ 596,049,920 ≈ **26.1%** (596,049,920 is the total computed from its
   config.json; its card lists 0.6B parameters, 0.44B without the embedding).

**What this means:** the vocabulary costs the same number of rows in both models, so it weighs far
more in the small one; tying the head halves what the large one would pay.

## What To Watch

- **vocab_size against the tokenizer.** The table can have more rows than the tokenizer has
  tokens, as in Qwen3.
- **tie_word_embeddings.** It decides whether the vocabulary is paid for once or twice.
- **Non-embedding counts.** Model cards often give both; compare models on the one without the
  embedding.
