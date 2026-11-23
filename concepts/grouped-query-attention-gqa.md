---
id: concept/grouped-query-attention-gqa
type: concept
slug: grouped-query-attention-gqa
schema_version: 3.1
title: Grouped Query Attention (GQA)
category: attention
code: GQA
aliases: []
order: 2
short_description: Query heads are split into groups, and each group shares one key-value head.
example_models:
- Llama 2 70B
- Llama 3
- Mistral
- Qwen 2.5
- Gemma 2
tags:
- attention
- gqa
updated: '2026-11-21'
sources:
- url: https://arxiv.org/abs/2305.13245
  title: 'GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints (arXiv:2305.13245)'
  trust: 0.95
- url: https://github.com/meta-llama/llama/blob/main/MODEL_CARD.md
  title: Llama 2 model card (Meta)
  trust: 0.9
- url: https://github.com/meta-llama/llama3/blob/main/MODEL_CARD.md
  title: Llama 3 model card (Meta)
  trust: 0.9
- url: https://arxiv.org/abs/2310.06825
  title: Mistral 7B (arXiv:2310.06825)
  trust: 0.95
- url: https://arxiv.org/abs/2408.00118
  title: 'Gemma 2: Improving Open Language Models at a Practical Size (arXiv:2408.00118)'
  trust: 0.95
---

## What It Is

A middle ground introduced in 2023 by Joshua Ainslie, James Lee-Thorp, Michiel de Jong, Yury Zemlyanskiy, Federico Lebrón and Sumit Sanghai in the GQA paper (accepted at EMNLP 2023). Query heads are grouped, and each group shares one set of Key-Value heads. For example, 32 query heads with 8 KV heads means 4 query heads share each KV head. This reduces KV cache size by 4x while retaining most of MHA's quality.

## Why It Matters

The key-value (KV) cache holds one key and one value per KV head for every token, so fewer KV heads mean a smaller cache during generation. The Mistral 7B authors use GQA for faster inference; the Llama 3 model card states it is used in all Llama 3 models to improve inference efficiency.

## Trade-off

GQA sits between multi-head attention (one KV head per query head) and multi-query attention (one KV head in total). The GQA authors report that uptrained GQA reaches quality close to multi-head attention at a speed comparable to multi-query attention.
