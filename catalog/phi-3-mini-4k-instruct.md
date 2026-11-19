---
id: model/phi-3-mini-4k-instruct
type: model
slug: phi-3-mini-4k-instruct
schema_version: 1.1
title: Phi-3 Mini 4K Instruct
organization: organization/microsoft
release_date: '2024-04-23'
release_year: 2024
release_month: 4
license: license/mit
signal_strength: high
architecture_id: architecture-era/transformer
model_category: language
parameters_active_b: 3.82
total_parameters: 3821079552
context_window_tokens: 4096
summary: Dense decoder-only transformer, ~3.8B parameters, 4K context, using multi-head attention, SwiGLU, RMSNorm and RoPE.
why_it_matters: Proved that data quality — not scale — can put a 3.8B model in the same benchmark range as models twice its size, under a permissive MIT license.
fingerprint:
  attention_type: MHA
  num_layers: 32
  attention_heads: 32
  kv_heads: 32
  head_size: 96
  hidden_size: 3072
  intermediate_size: 8192
  context_window_tokens: 4096
  vocabulary_size: 32064
  activation_function: SwiGLU
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000
  parameters_active_b: 3.82
  kv_cache_rating: medium
  key_detail: Full multi-head attention (32 query = 32 KV heads, no GQA); fused qkv_proj and gate_up_proj; Llama-compatible tensor layout; 4K context variant (a separate 128K variant uses LongRoPE)
moe_config: null
filters:
  size_category: small
  is_quantized: false
  quant_format: original
  is_moe: false
  pipeline_tag: text-generation
  attention_type: MHA
  is_gated: false
  library: transformers
hub_signals:
  downloads_30d: 317278
  likes: 1492
  last_modified: '2025-12-10'
  observed_at: '2026-11-19'
  source: https://huggingface.co/microsoft/Phi-3-mini-4k-instruct
aliases:
- microsoft/Phi-3-mini-4k-instruct
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
- benchmarks
- body
tags:
- llm
- phi
- microsoft
- dense
- mha
- instruct
- small
- mit
updated: '2026-11-19'
sources:
- url: https://huggingface.co/microsoft/Phi-3-mini-4k-instruct
  title: Phi-3-mini-4k-instruct — model card + config.json
  trust: 0.9
- url: https://arxiv.org/abs/2404.14219
  title: Phi-3 Technical Report
  trust: 0.95
- url: https://huggingface.co/microsoft/Phi-3-mini-4k-instruct/blob/main/config.json
  title: microsoft/Phi-3-mini-4k-instruct — config.json
  trust: 0.9
related:
- architecture-era/transformer
- license/mit
---

## Summary

Phi-3 Mini 4K Instruct is a dense, decoder-only transformer with 32 layers and a 3072-dimensional
residual stream. Unusually for a 2024 model, it uses full multi-head attention — 32 query heads and 32
key/value heads, head size 96, with no grouped-query sharing — alongside SwiGLU feed-forward blocks
(intermediate size 8192), RMSNorm, and rotary positional embeddings. The reference implementation
fuses the query/key/value projections into a single `qkv_proj` and the gate/up projections into a
single `gate_up_proj`, and adopts a Llama-compatible tensor layout so it drops into existing tooling.

## Why It Matters

Phi-3 Mini is the flagship result of Microsoft's "textbooks are all you need" thesis: at only 3.8B
parameters, trained on a heavily filtered and synthetically augmented "data-optimal" corpus, it lands
near Mixtral 8x7B and GPT-3.5 on MMLU (68.8%) and GSM8K (82.5%) despite being an order of magnitude
smaller in active compute. It reframed the scaling conversation around data quality rather than raw
token count or parameter count, and its small footprint plus MIT license made it a favorite for
on-device and edge deployment.

## Notes

Because it uses full MHA rather than GQA, its KV cache is larger per token than a same-size GQA model —
a deliberate trade at 4K context where the absolute cache stays small. This is the 4K-context variant;
a sibling `Phi-3-mini-128k-instruct` extends context using LongRoPE. The permissive MIT license (see
[[license/mit]]) imposes no field-of-use or scale restrictions, a notable contrast with the
community/custom licenses of many peers. Chat formatting uses the `<|user|>` / `<|assistant|>`
template. See [[architecture-era/transformer]] for the architecture lineage.
