---
id: model/mixtral-8x7b-instruct-v0-1
type: model
slug: mixtral-8x7b-instruct-v0-1
schema_version: 1.1
title: Mixtral 8x7B Instruct v0.1
organization: organization/mistral-ai
release_date: '2023-12-11'
release_year: 2023
release_month: 12
license: license/apache-2-0
signal_strength: high
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
parameters_active_b: 12.9
total_parameters: 46702792704
context_window_tokens: 32768
summary: Sparse mixture-of-experts transformer, ~46.7B total / ~12.9B active parameters, 8 experts top-2, 32K context, using GQA, SwiGLU, RMSNorm and RoPE.
why_it_matters: The first open sparse-MoE model to reach GPT-3.5-class quality, proving MoE could ship at a practical size under a permissive license.
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 14336
  context_window_tokens: 32768
  vocabulary_size: 32000
  activation_function: SwiGLU
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000
  parameters_active_b: 12.9
  kv_cache_rating: medium
  key_detail: Every FFN is replaced by 8 experts; a per-token router selects top-2 experts (intermediate_size 14336 per expert); ~46.7B total but only ~12.9B active per token
moe_config:
  expert_count: 8
  active_experts: 2
  shared_expert: false
  notes: Top-2 routing over 8 experts per layer; router is a linear gate over the 4096-dim hidden state; no shared/always-on expert
filters:
  size_category: large
  is_quantized: false
  quant_format: original
  is_moe: true
  pipeline_tag: text-generation
  attention_type: GQA
  is_gated: false
  library: vllm
hub_signals:
  downloads_30d: 316000
  likes: 4781
  last_modified: '2025-07-24'
  observed_at: '2026-11-20'
  source: https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1
aliases:
- mistralai/Mixtral-8x7B-Instruct-v0.1
- mixtral-8x7b-instruct-v0.1
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
- mistral
- moe
- sparse
- gqa
- instruct
- apache-2-0
updated: '2026-11-20'
sources:
- url: https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1
  title: Mixtral-8x7B-Instruct-v0.1 — model card + config.json
  trust: 0.9
- url: https://arxiv.org/abs/2401.04088
  title: Mixtral of Experts (Jiang et al., 2024)
  trust: 0.95
- url: https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json
  title: mistralai/Mixtral-8x7B-Instruct-v0.1 — config.json
  trust: 0.9
related:
- architecture-era/mixture-of-experts
- license/apache-2-0
---

## Summary

Mixtral 8x7B Instruct is a sparse mixture-of-experts transformer with 32 layers and a 4096-dimensional
residual stream. The attention path is identical to Mistral 7B — grouped-query attention with 32 query
heads sharing 8 key/value heads, RMSNorm, and RoPE (base frequency 1000000) — but every feed-forward
block is replaced by eight parallel SwiGLU experts (intermediate size 14336 each). A per-layer linear
router scores the 4096-dim hidden state and dispatches each token to its top-2 experts, whose outputs
are combined by the router's softmax weights. The result is ~46.7B total parameters of which only
~12.9B are active for any given token.

## Why It Matters

Mixtral was the model that brought sparse mixture-of-experts into the open-weight mainstream. It
matched or beat Llama 2 70B and GPT-3.5 on most benchmarks while activating roughly a fifth of its
parameters per token, demonstrating the core MoE bargain: pay 70B-class training and memory cost, but
only 13B-class compute at inference. The "8x7B" name is a useful fiction — the experts share attention
and embeddings, so the total is 46.7B rather than 8×7B=56B. It made the memory-vs-compute tradeoff of
MoE concrete for practitioners and seeded an entire generation of open MoE work.

## Notes

MoE serving has a distinctive cost profile: all 46.7B parameters must be resident in memory (roughly
90GB+ in bf16) even though only ~12.9B are used per token, so Mixtral is memory-bound rather than
compute-bound — quantization and expert-offloading are the usual levers to fit it on smaller hardware.
The router adds a small auxiliary load-balancing loss during training to keep experts evenly utilized.
The Apache-2.0 license applies with an explicit patent grant (see [[license/apache-2-0]]). Chat
formatting uses the `[INST] ... [/INST]` template. See [[architecture-era/mixture-of-experts]] for the
architecture lineage.
