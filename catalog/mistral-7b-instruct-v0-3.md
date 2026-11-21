---
id: model/mistral-7b-instruct-v0-3
type: model
slug: mistral-7b-instruct-v0-3
schema_version: 1.1
title: Mistral 7B Instruct v0.3
organization: organization/mistral-ai
release_date: '2024-05-22'
release_year: 2024
release_month: 5
license: license/apache-2-0
signal_strength: high
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 7.25
total_parameters: 7248023552
context_window_tokens: 32768
summary: Dense decoder-only transformer, ~7.25B parameters, 32K context, using GQA, SwiGLU, RMSNorm and RoPE.
why_it_matters: The Apache-2.0 workhorse of the open 7B tier and the base most early open fine-tunes were built on.
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 14336
  context_window_tokens: 32768
  vocabulary_size: 32768
  activation_function: SwiGLU
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000
  parameters_active_b: 7.25
  kv_cache_rating: medium
  key_detail: 'v0.3 extends the vocab to 32768 and disables sliding-window attention (sliding_window: null); rope_theta raised to 1000000'
moe_config: null
filters:
  size_category: small
  is_quantized: false
  quant_format: original
  is_moe: false
  pipeline_tag: text-generation
  attention_type: GQA
  is_gated: false
  library: vllm
hub_signals:
  downloads_30d: 1808627
  likes: 3844
  last_modified: '2025-12-03'
  observed_at: '2026-11-20'
  source: https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.3
aliases:
- mistralai/Mistral-7B-Instruct-v0.3
- mistral-7b-instruct-v0.3
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
- architecture_id
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
- body
tags:
- llm
- mistral
- dense
- gqa
- instruct
- apache-2-0
updated: '2026-11-20'
sources:
- url: https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.3
  title: Mistral 7B Instruct v0.3 — model card + config.json
  trust: 0.9
- url: https://arxiv.org/abs/2310.06825
  title: Mistral 7B (Jiang et al., 2023)
  trust: 0.95
- url: https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.3/blob/main/config.json
  title: mistralai/Mistral-7B-Instruct-v0.3 — config.json
  trust: 0.9
related:
- architecture-era/transformer
- license/apache-2-0
---

## Summary

Mistral 7B Instruct v0.3 is a dense, decoder-only transformer with 32 layers and a 4096-dimensional
residual stream. It uses grouped-query attention (32 query heads sharing 8 key/value heads, head
size 128), SwiGLU feed-forward blocks with an intermediate size of 14336, RMSNorm pre-normalization,
and rotary positional embeddings. The v0.3 revision extends the tokenizer vocabulary to 32768,
raises the RoPE base frequency to 1000000, and — unlike the original Mistral 7B — ships with
sliding-window attention disabled, so every layer attends over the full 32K context.

## Why It Matters

Mistral 7B was the model that made the open 7B tier competitive: at launch it outperformed Llama 2
13B across most benchmarks while being roughly half the size, and its permissive Apache-2.0 license
(with an explicit patent grant) made it the default base for a wave of community fine-tunes. The v0.3
instruct release consolidated those gains with a larger vocabulary and native function-calling
support in the tokenizer. Its parameter count follows the standard dense formula: a 32768×4096 input
embedding and an untied output head, 32 GQA attention blocks (~41.9M parameters each), and 32 SwiGLU
feed-forward blocks (~176.2M each), summing to ≈7.25B with the feed-forward layers dominating.

## Notes

The original Mistral 7B popularized sliding-window attention (a 4096-token local window) to bound the
KV cache; v0.2 and v0.3 set `sliding_window: null`, reverting to dense full-context attention, so the
"sliding window" association no longer applies to this checkpoint. Recommended chat formatting uses
the `[INST] ... [/INST]` template; the v0.3 tokenizer adds dedicated tokens for tool calls. The model
runs comfortably quantized on a single 8–16GB GPU. See [[license/apache-2-0]] for licensing and
[[architecture-era/transformer]] for the architecture lineage.
