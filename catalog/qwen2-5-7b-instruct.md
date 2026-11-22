---
id: model/qwen2-5-7b-instruct
type: model
slug: qwen2-5-7b-instruct
schema_version: 1.1
title: Qwen2.5 7B Instruct
organization: organization/qwen
release_date: '2024-09-19'
release_year: 2024
release_month: 9
license: license/apache-2-0
signal_strength: high
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 7.61
total_parameters: 7615616512
context_window_tokens: 131072
summary: Dense decoder-only transformer, ~7.61B parameters, 128K context via YaRN, using GQA, SwiGLU, RMSNorm and RoPE.
why_it_matters: The strongest Apache-2.0 model at the 7B tier, and the multilingual default that displaced Llama 3.1 8B for many builders.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 28
  kv_heads: 4
  head_size: 128
  hidden_size: 3584
  intermediate_size: 18944
  context_window_tokens: 131072
  vocabulary_size: 152064
  activation_function: SwiGLU
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 1000000
  parameters_active_b: 7.61
  kv_cache_rating: low
  key_detail: Only 4 KV heads (aggressive GQA); QKV projections carry bias; 128K context via YaRN on a 32768-trained base
moe_config: null
filters:
  size_category: small
  is_quantized: false
  quant_format: original
  is_moe: false
  pipeline_tag: text-generation
  attention_type: GQA
  is_gated: false
  library: transformers
hub_signals:
  downloads_30d: 6391398
  likes: 2505
  last_modified: '2025-01-12'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2.5-7B-Instruct
variants:
- name: Qwen/Qwen2.5-7B-Instruct-AWQ
  format: awq
  address: https://huggingface.co/Qwen/Qwen2.5-7B-Instruct-AWQ
  publisher: Qwen
aliases:
- Qwen/Qwen2.5-7B-Instruct
- qwen2.5-7b-instruct
- qwen2.5-7b-instruct-awq
lineage:
  parents: []
  children: []
locked:
- title
- organization
- release_date
- release_year
- release_month
- license
- signal_strength
- model_category
- parameters_active_b
- total_parameters
- context_window_tokens
- summary
- why_it_matters
- fingerprint.attention_type
- fingerprint.num_layers
- fingerprint.attention_heads
- fingerprint.kv_heads
- fingerprint.head_size
- fingerprint.hidden_size
- fingerprint.intermediate_size
- fingerprint.context_window_tokens
- fingerprint.vocabulary_size
- fingerprint.activation_function
- fingerprint.normalization
- fingerprint.positional_encoding
- fingerprint.rope_theta
- fingerprint.parameters_active_b
- fingerprint.kv_cache_rating
- fingerprint.key_detail
- moe_config
- filters.size_category
- filters.is_quantized
- filters.quant_format
- filters.is_moe
- filters.pipeline_tag
- filters.attention_type
- benchmarks
- body
tags:
- llm
- qwen
- alibaba
- dense
- gqa
- instruct
- multilingual
- apache-2-0
updated: '2026-11-22'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-7B-Instruct
  title: Qwen2.5-7B-Instruct — model card + config.json
  trust: 0.9
- url: https://arxiv.org/abs/2412.15115
  title: Qwen2.5 Technical Report
  trust: 0.95
- url: https://huggingface.co/Qwen/Qwen2.5-7B-Instruct/blob/main/config.json
  title: Qwen/Qwen2.5-7B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2309.00071
  title: arXiv:2309.00071
  trust: 0.95
- url: https://arxiv.org/abs/2407.10671
  title: arXiv:2407.10671
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen2/modeling_qwen2.py
  title: 'transformers/models/qwen2/modeling_qwen2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
related:
- architecture-era/transformer
- license/apache-2-0
---

## Summary

Qwen2.5 7B Instruct is a dense, decoder-only transformer with 28 layers and a 3584-dimensional
residual stream. It uses grouped-query attention with an unusually aggressive ratio — 28 query heads
sharing just 4 key/value heads (head size 128) — plus SwiGLU feed-forward blocks (intermediate size
18944), RMSNorm pre-normalization, and rotary positional embeddings with a base frequency of
1000000. Following the Qwen2 lineage, the query/key/value projections carry additive bias terms, a
departure from the bias-free projections used by Llama and Mistral. The 128K-token context window is
obtained by applying YaRN RoPE scaling at inference on top of a 32768-token-trained base.

## Why It Matters

Qwen2.5 raised the ceiling at the 7B tier: on knowledge, math, and code it outperforms Llama 3.1 8B
while remaining Apache-2.0 licensed, making it a genuinely permissive drop-in for commercial use. Its
strong multilingual coverage (29+ languages) and long-context support made it the default open small
model for many builders through 2025. The four-KV-head design keeps the KV cache small, which is a
material advantage for long-context serving and batched inference — at 128K tokens the cache is a
fraction of what an eight-KV-head model of similar size would require.

## Notes

The base `config.json` reports `max_position_embeddings: 32768`; the advertised 128K context requires
enabling YaRN scaling (set in `rope_scaling`) at load time — running without it caps effective context
at 32K. The tokenizer vocabulary is 152064 tokens with strong CJK coverage. Recommended usage follows
the ChatML template with `<|im_start|>` / `<|im_end|>` role markers. See [[license/apache-2-0]] for
licensing and [[architecture-era/transformer]] for the architecture lineage.
