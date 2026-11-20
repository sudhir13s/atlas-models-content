---
id: model/gemma-2-9b-it
type: model
slug: gemma-2-9b-it
schema_version: 1.1
title: Gemma 2 9B IT
organization: organization/google-deepmind
release_date: '2024-06-27'
release_year: 2024
release_month: 6
license: license/gemma
signal_strength: high
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 9.24
total_parameters: 9241705984
context_window_tokens: 8192
summary: Dense decoder-only transformer, ~9.24B parameters, 8K context, using GQA, alternating local/global attention, logit soft-capping, GeGLU, RMSNorm and RoPE.
why_it_matters: Google's strongest small open model of 2024, and the reference example of alternating local/global attention with attention soft-capping.
fingerprint:
  attention_type: GQA
  num_layers: 42
  attention_heads: 16
  kv_heads: 8
  head_size: 256
  hidden_size: 3584
  intermediate_size: 14336
  context_window_tokens: 8192
  vocabulary_size: 256000
  activation_function: GeGLU
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000
  parameters_active_b: 9.24
  kv_cache_rating: medium
  key_detail: Alternates local sliding-window (4096) and global attention every other layer; attn logit soft-cap 50.0, final logit soft-cap 30.0; head_dim 256 (decoupled from hidden_size); pre- and post-RMSNorm on each sublayer
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
  downloads_30d: 170370
  likes: 1171
  last_modified: '2024-08-27'
  observed_at: '2026-11-20'
  source: https://huggingface.co/google/gemma-2-9b-it
aliases:
- google/gemma-2-9b-it
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
- gemma
- google
- dense
- gqa
- instruct
- sliding-window
updated: '2026-11-20'
sources:
- url: https://huggingface.co/google/gemma-2-9b-it
  title: Gemma 2 9B IT — model card
  trust: 0.9
- url: https://arxiv.org/abs/2408.00118
  title: 'Gemma 2: Improving Open Language Models at a Practical Size'
  trust: 0.95
- url: https://arxiv.org/abs/2009.03300
  title: arXiv:2009.03300
  trust: 0.95
- url: https://arxiv.org/abs/1905.07830
  title: arXiv:1905.07830
  trust: 0.95
- url: https://arxiv.org/abs/1911.11641
  title: arXiv:1911.11641
  trust: 0.95
- url: https://arxiv.org/abs/1904.09728
  title: arXiv:1904.09728
  trust: 0.95
- url: https://arxiv.org/abs/1905.10044
  title: arXiv:1905.10044
  trust: 0.95
- url: https://arxiv.org/abs/1907.10641
  title: arXiv:1907.10641
  trust: 0.95
- url: https://arxiv.org/abs/1811.00937
  title: arXiv:1811.00937
  trust: 0.95
- url: https://arxiv.org/abs/1809.02789
  title: arXiv:1809.02789
  trust: 0.95
- url: https://arxiv.org/abs/1911.01547
  title: arXiv:1911.01547
  trust: 0.95
- url: https://arxiv.org/abs/1705.03551
  title: arXiv:1705.03551
  trust: 0.95
- url: https://arxiv.org/abs/2107.03374
  title: arXiv:2107.03374
  trust: 0.95
- url: https://arxiv.org/abs/2108.07732
  title: arXiv:2108.07732
  trust: 0.95
- url: https://arxiv.org/abs/2110.14168
  title: arXiv:2110.14168
  trust: 0.95
- url: https://arxiv.org/abs/2009.11462
  title: arXiv:2009.11462
  trust: 0.95
- url: https://arxiv.org/abs/2101.11718
  title: arXiv:2101.11718
  trust: 0.95
- url: https://arxiv.org/abs/2110.08193
  title: arXiv:2110.08193
  trust: 0.95
- url: https://arxiv.org/abs/1804.09301
  title: arXiv:1804.09301
  trust: 0.95
- url: https://arxiv.org/abs/2109.07958
  title: arXiv:2109.07958
  trust: 0.95
- url: https://arxiv.org/abs/1804.06876
  title: arXiv:1804.06876
  trust: 0.95
- url: https://arxiv.org/abs/2103.03874
  title: arXiv:2103.03874
  trust: 0.95
- url: https://arxiv.org/abs/2304.06364
  title: arXiv:2304.06364
  trust: 0.95
- url: https://arxiv.org/abs/2206.04615
  title: arXiv:2206.04615
  trust: 0.95
- url: https://arxiv.org/abs/2203.09509
  title: arXiv:2203.09509
  trust: 0.95
related:
- architecture-era/transformer
- license/gemma
---

## Summary

Gemma 2 9B IT is a dense, decoder-only transformer with 42 layers and a 3584-dimensional residual
stream. It uses grouped-query attention (16 query heads sharing 8 key/value heads) with a head
dimension of 256 that is deliberately decoupled from the hidden size, GeGLU feed-forward blocks
(intermediate size 14336), and rotary positional embeddings. Two architectural signatures set it
apart: attention layers alternate between a local sliding window of 4096 tokens and full global
attention every other layer, and both the attention logits (soft-capped at 50.0) and the final output
logits (soft-capped at 30.0) are passed through a `tanh` soft-cap to stabilize training. Each sublayer
is wrapped in both pre- and post-RMSNorm.

## Why It Matters

Gemma 2 9B was Google's answer at the sub-10B open tier, beating Llama 3 8B on most knowledge and
reasoning benchmarks and shipping with knowledge-distillation-trained weights (the 9B was distilled
from a larger teacher rather than trained from scratch on tokens alone). Architecturally it is the
canonical reference for two ideas that later spread widely: interleaving cheap local attention with
periodic global attention to bound compute, and logit soft-capping to keep attention scores and output
distributions from saturating. The 256-dimensional heads (larger than the 128 typical of peers) give
each head more representational capacity.

## Notes

The weights are gated on Hugging Face and governed by the custom Gemma Terms of Use (commercial use
permitted, subject to a prohibited-use policy — see [[license/gemma]]). The 8K context is short by
2024 standards and is a real constraint for long-document RAG. The benchmark figures above are the
Gemma 2 report's numbers for the 9B *pretrained base*; the instruction-tuned checkpoint's scores
differ. Soft-capping must be enabled for faithful inference — some early inference stacks silently
dropped it, degrading quality. See [[architecture-era/transformer]] for the architecture lineage.
