---
id: model/gemma-2-9b-it
type: model
slug: gemma-2-9b-it
schema_version: 3.0
title: Gemma 2 9B IT
organization: organization/google-deepmind
release_date: '2024-06-27'
license: license/gemma
signal_strength: high
architecture_id: architecture-era/llama-recipe
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
parameters_active_b: 9.24
total_parameters: 9241705984
context_window_tokens: 8192
summary: Dense decoder-only transformer, ~9.24B parameters, 8K context, using GQA, alternating local/global attention, logit soft-capping, GeGLU, RMSNorm and RoPE.
why_it_matters: Google's card says the relatively small size of Gemma models makes it possible to deploy them on a laptop, desktop or your own cloud infrastructure, and the Gemma 2 report says the 9B model was trained with knowledge distillation instead of next-token prediction.
fingerprint:
  attention_type: GQA
  num_layers: 42
  attention_heads: 16
  kv_heads: 8
  head_size: 256
  hidden_size: 3584
  intermediate_size: 14336
  vocabulary_size: 256000
  activation_function: GeGLU
  normalization: RMSNorm
  norm_placement: both
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 10000
  kv_cache_rating: medium
  key_detail: Alternates local sliding-window (4096) and global attention every other layer; attn logit soft-cap 50.0, final logit soft-cap 30.0; head_dim 256 (decoupled from hidden_size); pre- and post-RMSNorm on each sublayer
filters:
  size_category: small
  is_quantized: false
  quant_format: original
  is_moe: false
  pipeline_tag: text-generation
  is_gated: true
  library: transformers
hub_signals:
  downloads_30d: 170370
  likes: 1171
  last_modified: '2024-08-27'
  observed_at: '2026-11-20'
  source: https://huggingface.co/google/gemma-2-9b-it
gguf_repositories:
  repositories:
  - publisher: bartowski
    repo_id: bartowski/gemma-2-9b-it-GGUF
    address: https://huggingface.co/bartowski/gemma-2-9b-it-GGUF
  observed_at: '2026-11-23'
  source: https://huggingface.co/models?library=gguf&other=base_model:quantized:google/gemma-2-9b-it
aliases:
- google/gemma-2-9b-it
lineage:
  parents: []
  children: []
locked:
- title
- organization
- release_date
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
- fingerprint.vocabulary_size
- fingerprint.activation_function
- fingerprint.normalization
- fingerprint.positional_encoding
- fingerprint.rope_theta
- fingerprint.kv_cache_rating
- fingerprint.key_detail
- moe_config
- filters.size_category
- filters.is_quantized
- filters.quant_format
- filters.is_moe
- filters.pipeline_tag
- body
tags:
- gemma
- google
- instruct
- sliding-window
updated: '2026-11-23'
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
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gemma2/modeling_gemma2.py
  title: 'transformers/models/gemma2/modeling_gemma2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
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

Google's model card describes Gemma as a family of lightweight open models built from the same
research and technology used to create the Gemini models. It says their relatively small size makes
it possible to deploy them in environments with limited resources, such as a laptop, a desktop or
your own cloud infrastructure, and that the 9B model was trained on 8 trillion tokens.

The Gemma 2 report says the 2B and 9B models were trained with knowledge distillation instead of
next-token prediction, and that the architecture interleaves local and global attention and uses
grouped-query attention. The report's authors claim the resulting models deliver the best
performance for their size.

## Notes

The weights are gated on Hugging Face and governed by the custom Gemma Terms of Use (commercial use
permitted, subject to a prohibited-use policy — see [[license/gemma]]). See
[[architecture-era/transformer]] for the architecture lineage.
