---
id: model/qwen3-4b-thinking-2507
type: model
slug: qwen3-4b-thinking-2507
schema_version: 3.0
title: Qwen3-4B-Thinking-2507
organization: organization/qwen
release_date: '2025-08-05'
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
- task/reasoning-models
parameters_active_b: 4.02
total_parameters: 4022468096
context_window_tokens: 262144
summary: Qwen3-4B-Thinking-2507 is a 4-billion-parameter language model from Qwen that writes out its reasoning before it answers. The card says this release continues to scale the thinking capability of Qwen3-4B, improving the quality and depth of its reasoning, and that it has a native context of 262,144 tokens.
intended_use: The card says this version has an increased thinking length and that Qwen strongly recommends it for highly complex reasoning tasks. It lists improved reasoning in logical reasoning, mathematics, science and coding, and better instruction following, tool use and text generation.
what_sets_it_apart: The card says the model supports only thinking mode, so enabling it is no longer needed, and that the default chat template automatically includes the opening thinking tag. It is therefore normal for the output to contain only a closing thinking tag.
running_it: The card suggests Temperature=0.6, TopP=0.95, TopK=20 and MinP=0, and an output length of 32,768 tokens for most queries, or 81,920 tokens for highly complex math and programming problems.
fingerprint:
  attention_type: GQA
  num_layers: 36
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 2560
  intermediate_size: 9728
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 5000000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 301229
  likes: 630
  last_modified: '2025-08-06'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-4B-Thinking-2507
aliases:
- Qwen/Qwen3-4B-Thinking-2507
lineage:
  parents: []
  children: []
quotes:
  summary: Over the past three months, we have continued to scale the **thinking capability** of Qwen3-4B, improving both the **quality and depth** of reasoning.
  intended_use: This version has an increased thinking length.
  what_sets_it_apart: Additionally, to enforce model thinking, the default chat template automatically includes `<think>`.
  running_it: We suggest using `Temperature=0.6`, `TopP=0.95`, `TopK=20`, and `MinP=0`.
tags:
- qwen3
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-4B-Thinking-2507
  title: Qwen/Qwen3-4B-Thinking-2507 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-4B-Thinking-2507/blob/main/config.json
  title: Qwen/Qwen3-4B-Thinking-2507 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'transformers/models/qwen3/modeling_qwen3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


