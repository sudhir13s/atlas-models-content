---
id: model/qwen3-30b-a3b-instruct-2507
type: model
slug: qwen3-30b-a3b-instruct-2507
schema_version: 1.1
title: Qwen3-30B-A3B-Instruct-2507
organization: organization/qwen
release_date: '2025-07-28'
release_year: 2025
release_month: 7
license: license/apache-2-0
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
total_parameters: 30532122624
context_window_tokens: 262144
summary: 'Qwen3-30B-A3B-Instruct-2507 is a language model from Qwen, an updated release of the non-thinking mode of Qwen3-30B-A3B, so it answers directly without writing out its reasoning first. It is a Mixture-of-Experts model, which uses only some of its parameters for each token: 30.5 billion in total with 3.3 billion activated, and a native context of 262,144 tokens.'
what_sets_it_apart: Qwen lists improvements in instruction following, logical reasoning, text comprehension, mathematics, science, coding and tool use, broader coverage of long-tail knowledge across languages, better alignment with user preferences on open-ended tasks, and stronger 256K long-context understanding. The card says the model supports only non-thinking mode and does not produce thinking blocks.
running_it: The card suggests Temperature=0.7, TopP=0.8, TopK=20 and MinP=0, and an output length of 16,384 tokens for most queries. It notes that specifying enable_thinking=False is no longer required.
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 32
  kv_heads: 4
  head_size: 128
  hidden_size: 2048
  intermediate_size: 6144
  context_window_tokens: 262144
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 10000000.0
moe_config:
  expert_count: 128
  active_experts: 8
filters:
  size_category: large
  is_quantized: false
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 713594
  likes: 856
  last_modified: '2025-09-17'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-30B-A3B-Instruct-2507
aliases:
- Qwen/Qwen3-30B-A3B-Instruct-2507
lineage:
  parents: []
  children: []
quotes:
  summary: 'We introduce the updated version of the **Qwen3-30B-A3B non-thinking mode**, named **Qwen3-30B-A3B-Instruct-2507**, featuring the following key enhancements:'
  what_sets_it_apart: '**Enhanced capabilities** in **256K long-context understanding**.'
  running_it: We suggest using `Temperature=0.7`, `TopP=0.8`, `TopK=20`, and `MinP=0`.
tags:
- llm
- text-generation
- qwen3-moe
- moe
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-30B-A3B-Instruct-2507
  title: Qwen/Qwen3-30B-A3B-Instruct-2507 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-30B-A3B-Instruct-2507/blob/main/config.json
  title: Qwen/Qwen3-30B-A3B-Instruct-2507 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2402.17463
  title: arXiv:2402.17463
  trust: 0.95
- url: https://arxiv.org/abs/2407.02490
  title: arXiv:2407.02490
  trust: 0.95
- url: https://arxiv.org/abs/2501.15383
  title: arXiv:2501.15383
  trust: 0.95
- url: https://arxiv.org/abs/2404.06654
  title: arXiv:2404.06654
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3_moe/modeling_qwen3_moe.py
  title: 'transformers/models/qwen3_moe/modeling_qwen3_moe.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Qwen3-30B-A3B-Instruct-2507 is a language model from Qwen, an updated release of the non-thinking mode of Qwen3-30B-A3B, so it answers directly without writing out its reasoning first. It is a Mixture-of-Experts model, which uses only some of its parameters for each token: 30.5 billion in total with 3.3 billion activated, and a native context of 262,144 tokens.

## What Sets It Apart

Qwen lists improvements in instruction following, logical reasoning, text comprehension, mathematics, science, coding and tool use, broader coverage of long-tail knowledge across languages, better alignment with user preferences on open-ended tasks, and stronger 256K long-context understanding. The card says the model supports only non-thinking mode and does not produce thinking blocks.

## Running It

The card suggests Temperature=0.7, TopP=0.8, TopK=20 and MinP=0, and an output length of 16,384 tokens for most queries. It notes that specifying enable_thinking=False is no longer required.
