---
id: model/deepseek-v3
type: model
slug: deepseek-v3
schema_version: 1.1
title: DeepSeek-V3
organization: organization/deepseek
release_date: '2024-12-26'
release_year: 2024
release_month: 12
license: license/deepseek
signal_strength: high
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
parameters_active_b: 37
total_parameters: 671026419200
context_window_tokens: 131072
summary: Sparse mixture-of-experts transformer, ~671B total / ~37B active parameters, 128K context, using Multi-head Latent Attention (MLA), DeepSeekMoE, SwiGLU, RMSNorm and RoPE.
why_it_matters: The open-weight model that matched frontier closed systems in late 2024 — and did it with MLA and an auxiliary-loss-free MoE at a fraction of the training cost.
fingerprint:
  attention_type: MLA
  num_layers: 61
  attention_heads: 128
  kv_heads: 128
  head_size: 128
  hidden_size: 7168
  intermediate_size: 18432
  context_window_tokens: 131072
  vocabulary_size: 129280
  activation_function: SwiGLU
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000
  parameters_active_b: 37
  kv_cache_rating: low
  key_detail: Multi-head Latent Attention compresses KV to a 512-dim latent (kv_lora_rank 512, q_lora_rank 1536) with decoupled RoPE (qk_rope_head_dim 64 + qk_nope_head_dim 128, v_head_dim 128); DeepSeekMoE with 256 routed + 1 shared expert, top-8, first 3 layers dense; 128K context via YaRN (config max_position_embeddings 163840); auxiliary-loss-free load balancing; native FP8 training
moe_config:
  expert_count: 256
  active_experts: 8
  shared_expert: true
  notes: 'DeepSeekMoE: 1 always-on shared expert + 256 fine-grained routed experts (moe_intermediate_size 2048), top-8 routing via a sigmoid gate with bias-based auxiliary-loss-free balancing; first 3 layers use a dense FFN (first_k_dense_replace 3)'
filters:
  size_category: xlarge
  is_quantized: false
  quant_format: original
  is_moe: true
  pipeline_tag: text-generation
  attention_type: MLA
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
- deepseek
- moe
- sparse
- mla
- frontier
- fp8
updated: '2026-11-19'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-V3
  title: DeepSeek-V3 — model card + config.json
  trust: 0.9
- url: https://arxiv.org/abs/2412.19437
  title: DeepSeek-V3 Technical Report
  trust: 0.95
related:
- architecture-era/mixture-of-experts
- license/deepseek
---

## Summary

DeepSeek-V3 is a sparse mixture-of-experts transformer with 61 layers and a 7168-dimensional residual
stream, ~671B total parameters of which only ~37B are active per token. It combines two signature
DeepSeek innovations. First, **Multi-head Latent Attention (MLA)** replaces standard KV projections
with a low-rank joint compression: keys and values are projected into a 512-dimensional latent
(`kv_lora_rank`) and reconstructed on the fly, with position information carried by a decoupled RoPE
sub-vector (`qk_rope_head_dim` 64) alongside a content sub-vector (`qk_nope_head_dim` 128) — collapsing
the KV cache to a tiny fraction of a conventional 128-head model. Second, **DeepSeekMoE** replaces the
feed-forward block (outside the first three dense layers) with 1 always-on shared expert plus 256
fine-grained routed experts (intermediate size 2048 each), routing each token to its top-8 experts via
a sigmoid gate.

## Why It Matters

DeepSeek-V3 was the moment an open-weight model drew level with the closed frontier: it matched or beat
GPT-4o and Claude 3.5 Sonnet across knowledge, math, and code benchmarks (MMLU 88.5%, MMLU-Pro 75.9%,
MATH-500 90.2%). Just as important was *how cheaply* it was trained — the report puts full pretraining
at ~2.79M H800 GPU-hours, an order of magnitude below comparable frontier runs — enabled by native FP8
mixed-precision training, MLA's memory savings, and an **auxiliary-loss-free** load-balancing scheme
that steers experts with a learned per-expert bias instead of the usual balancing loss that degrades
quality. It reset expectations for what open models and modest budgets could achieve.

## Notes

The KV-cache efficiency of MLA is the headline serving advantage: despite 671B total parameters, its
per-token cache footprint is far below a dense 70B model, which is decisive for long-context, high-
throughput inference. Serving still requires the full parameter set resident (~671B in FP8, i.e.
hundreds of GB across multiple nodes), so it is a data-center model, not a laptop one. The config lists
`max_position_embeddings: 163840`; the officially supported/documented context is 128K via YaRN
scaling. Weights ship in FP8 (E4M3) with block-wise scaling. Licensing is the custom DeepSeek Model
License — commercially usable with use-based restrictions (see [[license/deepseek]]). See
[[architecture-era/mixture-of-experts]] for the architecture lineage.
