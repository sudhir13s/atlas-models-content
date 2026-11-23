---
id: model/qwen3-4b-instruct-2507
type: model
slug: qwen3-4b-instruct-2507
schema_version: 3.0
title: Qwen3-4B-Instruct-2507
organization: organization/qwen
release_date: '2025-08-05'
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
parameters_active_b: 4.02
total_parameters: 4022468096
context_window_tokens: 262144
summary: Qwen3-4B-Instruct-2507 is an updated version of the non-thinking mode of Qwen3-4B, from Qwen. It is a 4.0B-parameter causal language model that answers directly and does not write thinking blocks. The card gives a native context length of 262,144 tokens.
what_sets_it_apart: 'Qwen lists these changes over the earlier version: better general abilities (instruction following, logical reasoning, text comprehension, mathematics, science, coding and tool use), wider knowledge of rare topics across languages, closer alignment with user preferences on open-ended tasks, and improved understanding of 256K-token contexts. The card says the model supports only non-thinking mode, and the enable_thinking=False setting is no longer needed.'
running_it: The card suggests temperature 0.7, top_p 0.8, top_k 20 and min_p 0, and an output length of 16,384 tokens for most queries. A presence_penalty between 0 and 2 can reduce endless repetition but may cause language mixing. If memory runs out, it advises shortening the context length, for example to 32,768 tokens. It says Transformers versions before 4.51.0 fail with a KeyError for qwen3.
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
  downloads_30d: 2734698
  likes: 1061
  last_modified: '2025-09-17'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-4B-Instruct-2507
aliases:
- Qwen/Qwen3-4B-Instruct-2507
lineage:
  parents: []
  children: []
quotes:
  summary: 'We introduce the updated version of the **Qwen3-4B non-thinking mode**, named **Qwen3-4B-Instruct-2507**, featuring the following key enhancements:'
  what_sets_it_apart: This model supports only non-thinking mode and does not generate
  running_it: We suggest using `Temperature=0.7`, `TopP=0.8`, `TopK=20`, and `MinP=0`.
tags:
- qwen3
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-4B-Instruct-2507
  title: Qwen/Qwen3-4B-Instruct-2507 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-4B-Instruct-2507/blob/main/config.json
  title: Qwen/Qwen3-4B-Instruct-2507 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'transformers/models/qwen3/modeling_qwen3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


