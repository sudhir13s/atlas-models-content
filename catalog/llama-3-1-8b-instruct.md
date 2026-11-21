---
id: model/llama-3-1-8b-instruct
type: model
slug: llama-3-1-8b-instruct
schema_version: 1.1
title: Llama 3.1 8B Instruct
organization: organization/meta
release_date: '2024-07-23'
release_year: 2024
release_month: 7
license: license/llama-3-1
signal_strength: high
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 8.03
total_parameters: 8030261248
context_window_tokens: 131072
summary: Dense decoder-only transformer, 8B parameters, 128K context, using GQA, SwiGLU, RMSNorm and RoPE.
why_it_matters: Meta's most-downloaded open small model and the default local baseline at the 8B tier.
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 14336
  context_window_tokens: 131072
  vocabulary_size: 128256
  activation_function: SwiGLU
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 500000
  parameters_active_b: 8.03
  kv_cache_rating: medium
  key_detail: 128K context via RoPE scaling (factor 8); rope_theta 500000
moe_config: null
filters:
  size_category: small
  is_quantized: false
  quant_format: original
  is_moe: false
  pipeline_tag: text-generation
  attention_type: GQA
  is_gated: true
  library: transformers
hub_signals:
  downloads_30d: 4097176
  likes: 9047
  last_modified: '2024-09-25'
  observed_at: '2026-11-20'
  source: https://huggingface.co/meta-llama/Llama-3.1-8B-Instruct
aliases:
- meta-llama/Llama-3.1-8B-Instruct
- llama-3.1-8b-instruct
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
- meta
- llama
- dense
- gqa
- instruct
updated: '2026-11-20'
sources:
- url: https://huggingface.co/meta-llama/Llama-3.1-8B-Instruct
  title: Llama 3.1 8B Instruct — model card + config.json
  trust: 0.9
- url: https://arxiv.org/abs/2407.21783
  title: The Llama 3 Herd of Models
  trust: 0.95
- url: https://arxiv.org/abs/2204.05149
  title: arXiv:2204.05149
  trust: 0.95
related:
- architecture-era/transformer
---

## Summary

Llama 3.1 8B Instruct is a dense, decoder-only transformer with 32 layers and a 4096-dimensional
residual stream. It uses grouped-query attention (32 query heads sharing 8 key/value heads),
SwiGLU feed-forward blocks (intermediate size 14336), RMSNorm pre-normalization, and rotary
positional embeddings (RoPE, base frequency 500000). The 128K-token context is achieved by RoPE
scaling on top of an 8K-trained base.

## Why It Matters

It is the default open baseline at the 8B tier — capable enough for real assistants and RAG, small
enough to run quantized on an M-series laptop or an 8GB GPU. The instruct variant is aligned with
supervised fine-tuning plus rejection sampling and Direct Preference Optimization (DPO), which
Llama 3 adopted in place of PPO-based RLHF. Its parameter count verifies against the standard dense
transformer formula: a 128256×4096 embedding, 32 layers of GQA attention (~41.9M params each) and
SwiGLU FFN (~176.2M each), an untied output head, summing to ≈8.03B, with the feed-forward blocks
dominating (~70%).

## Notes

The model is gated on Hugging Face (manual approval). Recommended inference defaults from the model
card are temperature 0.6 and top_p 0.9. Pretraining used ~15.6T tokens — far past the Chinchilla
compute-optimal point — deliberately over-training for inference-time efficiency. See
[[hyperparameter/temperature]] for sampling guidance and [[architecture-era/transformer]] for the
architecture lineage.
