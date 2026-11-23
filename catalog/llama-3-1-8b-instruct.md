---
id: model/llama-3-1-8b-instruct
type: model
slug: llama-3-1-8b-instruct
schema_version: 3.0
title: Llama 3.1 8B Instruct
organization: organization/meta
release_date: '2024-07-23'
license: license/llama-3-1
signal_strength: high
architecture_id: architecture-era/llama-recipe
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
parameters_active_b: 8.03
total_parameters: 8030261248
context_window_tokens: 131072
summary: Dense decoder-only transformer, 8B parameters, 128K context, using GQA, SwiGLU, RMSNorm and RoPE.
why_it_matters: Meta's card says the Llama 3.1 instruction-tuned text models are optimized for multilingual dialogue and, by Meta's account, outperform many of the available open-source and closed chat models on common industry benchmarks.
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 14336
  vocabulary_size: 128256
  activation_function: SwiGLU
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 500000
  kv_cache_rating: medium
  key_detail: 128K context via RoPE scaling (factor 8); rope_theta 500000
filters:
  size_category: small
  is_quantized: false
  quant_format: original
  is_moe: false
  pipeline_tag: text-generation
  is_gated: true
  library: transformers
hub_signals:
  downloads_30d: 4097176
  likes: 9047
  last_modified: '2024-09-25'
  observed_at: '2026-11-20'
  source: https://huggingface.co/meta-llama/Llama-3.1-8B-Instruct
gguf_repositories:
  repositories:
  - publisher: unsloth
    repo_id: unsloth/Llama-3.1-8B-Instruct-GGUF
    address: https://huggingface.co/unsloth/Llama-3.1-8B-Instruct-GGUF
  - publisher: bartowski
    repo_id: bartowski/Meta-Llama-3.1-8B-Instruct-GGUF
    address: https://huggingface.co/bartowski/Meta-Llama-3.1-8B-Instruct-GGUF
  observed_at: '2026-11-23'
  source: https://huggingface.co/models?library=gguf&other=base_model:quantized:meta-llama/Llama-3.1-8B-Instruct
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
- meta
- llama
- instruct
updated: '2026-11-23'
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
- url: https://github.com/meta-llama/llama3/blob/main/llama/model.py
  title: 'meta-llama/llama3: llama/model.py (official reference implementation)'
  trust: 0.9
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

Meta's model card says the Llama 3.1 instruction-tuned text models are optimized for multilingual
dialogue use cases and, by Meta's account, outperform many of the available open-source and closed
chat models on common industry benchmarks. The card says the tuned versions use supervised
fine-tuning (SFT) and reinforcement learning with human feedback (RLHF), and that the Llama 3.1
licence allows using the models' outputs to improve other models, including synthetic data
generation and distillation.

The parameter count can be checked against the standard dense transformer formula: a 128256×4096
embedding, 32 layers of GQA attention (~41.9M params each) and
SwiGLU FFN (~176.2M each), an untied output head, summing to ≈8.03B, with the feed-forward blocks
dominating (~70%).

## Notes

The model is gated on Hugging Face (manual approval). Meta's card says Llama 3.1 was pretrained on
about 15 trillion tokens of data from publicly available sources, with a pretraining data cutoff of
December 2023. See [[hyperparameter/temperature]] for sampling guidance and
[[architecture-era/transformer]] for the architecture lineage.
