---
id: parameter/vocab-size
type: parameter
slug: vocab-size
schema_version: 1.0
title: "Vocabulary Size"
name: vocab_size
priority: 4
importance: critical
applies_to: [llm, transformer, moe]
range: "32k → 152k+"
controls: "The number of distinct tokens the tokenizer emits. It sets the height of both the embedding table and the output head."
formula: "embedding params = vocab_size × hidden_size (and again for the output head, unless tied)"
if_increased: "Shorter sequences (more text per token) and better multilingual/code coverage, but a larger embedding + output head and a softmax over more classes."
if_decreased: "Smaller embedding/head, but longer token sequences (more compute per document) and worse coverage of rare words and non-English scripts."
tags: [llm, transformer, tokenizer, vocabulary, embedding, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.95 }
---

## Definition
`vocab_size` (`V`) is the size of the tokenizer's vocabulary — how many distinct subword tokens the
model can represent. It fixes the height of the **embedding table** (which maps token IDs to vectors)
and of the **output head** (which maps the final hidden state back to a distribution over tokens).

## What to look for
Vocabulary size trades **sequence length against embedding cost**:

- Larger `V` → fewer tokens per document → less compute per training/inference pass, better coverage
  of code and non-English text — but a bigger embedding matrix and output softmax.
- Llama 2 used 32k; Llama 3 jumped to **128256** for stronger multilingual and code tokenization;
  Gemma uses 256k.

For small models the embedding table can be a large fraction of total parameters, which is exactly
why weight tying (sharing embedding and output head) is common in small models.

## Calculation
The embedding table is `vocab_size × hidden_size`. Unless embeddings are tied, the output (unembedding)
head is another `vocab_size × hidden_size`:

$$\text{embedding + head} = 2 \cdot V \cdot d \quad(\text{or } V\cdot d \text{ if tied})$$

## Example
Llama 3 8B: `V = 128256`, `d = 4096`. Embedding table = `128256 × 4096 ≈ 525.3M`. With an untied
output head that is another ≈525.3M, so token-related weights alone are ≈1.05B — about 13% of the 8B
model. In a 150M-parameter model the same-size embedding would be the majority of the network, which
is why small models tie the two.
