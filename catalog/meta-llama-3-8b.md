---
id: model/meta-llama-3-8b
type: model
slug: meta-llama-3-8b
schema_version: 1.1
title: Meta-Llama-3-8B
organization: organization/meta
release_date: '2024-04-17'
release_year: 2024
release_month: 4
license: license/llama-3
model_category: language
tasks:
- task/text-generation
total_parameters: 8030261248
summary: Meta Llama 3 8B is the 8-billion-parameter size of Meta's Llama 3 family of language models, which take text in and generate text and code. The card says it is an auto-regressive language model with an optimized transformer architecture, trained on more than 15 trillion tokens with an 8k-token context length.
intended_use: Meta says Llama 3 is intended for commercial and research use in English. Pretrained models such as this one can be adapted for a variety of natural language generation tasks, while the instruction-tuned versions are meant for assistant-like chat. Out-of-scope uses are anything that violates applicable laws or the Acceptable Use Policy, and languages other than English unless a developer fine-tunes the model for them.
what_sets_it_apart: The card says the 8B and 70B models use Grouped-Query Attention for better inference scalability, and that the training data is a new mix of publicly available online data with a knowledge cutoff of March 2023 for the 8B size.
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 14336
  activation_function: SwiGLU
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 500000
filters:
  size_category: medium
  is_gated: true
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 98730
  likes: 6808
  last_modified: '2024-09-27'
  observed_at: '2026-11-20'
  source: https://huggingface.co/meta-llama/Meta-Llama-3-8B
aliases:
- meta-llama/Meta-Llama-3-8B
lineage:
  parents: []
  children: []
quotes:
  summary: Llama 3 is an auto-regressive language model that uses an optimized transformer architecture.
  intended_use: Llama 3 is intended for commercial and research use in English.
  what_sets_it_apart: Both the 8 and 70B versions use Grouped-Query Attention (GQA) for improved inference scalability.
tags:
- llm
- text-generation
updated: '2026-11-23'
sources:
- url: https://huggingface.co/meta-llama/Meta-Llama-3-8B
  title: meta-llama/Meta-Llama-3-8B — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2407.21783
  title: The Llama 3 Herd of Models
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/modeling_llama.py
  title: 'transformers/models/llama/modeling_llama.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Meta Llama 3 8B is the 8-billion-parameter size of Meta's Llama 3 family of language models, which take text in and generate text and code. The card says it is an auto-regressive language model with an optimized transformer architecture, trained on more than 15 trillion tokens with an 8k-token context length.

## Intended Use

Meta says Llama 3 is intended for commercial and research use in English. Pretrained models such as this one can be adapted for a variety of natural language generation tasks, while the instruction-tuned versions are meant for assistant-like chat. Out-of-scope uses are anything that violates applicable laws or the Acceptable Use Policy, and languages other than English unless a developer fine-tunes the model for them.

## What Sets It Apart

The card says the 8B and 70B models use Grouped-Query Attention for better inference scalability, and that the training data is a new mix of publicly available online data with a knowledge cutoff of March 2023 for the 8B size.
