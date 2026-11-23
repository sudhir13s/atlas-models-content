---
id: model/qwen2-5-7b-instruct
type: model
slug: qwen2-5-7b-instruct
schema_version: 3.0
title: Qwen2.5 7B Instruct
organization: organization/qwen
release_date: '2024-09-19'
license: license/apache-2-0
signal_strength: high
architecture_id: architecture-era/llama-recipe
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
parameters_active_b: 7.61
total_parameters: 7615616512
context_window_tokens: 131072
base_models:
- name: Qwen/Qwen2.5-7B
  address: https://huggingface.co/Qwen/Qwen2.5-7B
summary: Qwen2.5 7B Instruct is a language model from Qwen that reads text and writes text in reply. It is the instruction-tuned member of the Qwen2.5 series at the 7B size, which means the base model was trained further to follow instructions and hold a conversation. The card says the Qwen2.5 series ranges from 0.5 to 72 billion parameters.
what_sets_it_apart: Qwen states that, compared with the earlier Qwen2 series, Qwen2.5 has more knowledge and improved coding and mathematics, follows instructions better, generates long texts of over 8K tokens, understands structured data such as tables and produces structured outputs such as JSON. The card also states support for a context of up to 128K tokens and for over 29 languages.
running_it: The model card says the published config.json is set for a context of up to 32,768 tokens, and that longer inputs rely on YaRN, a technique for extending the length a model handles. The card advises enabling it only when long contexts are needed, because the static form of YaRN may affect performance on shorter texts.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 28
  kv_heads: 4
  head_size: 128
  hidden_size: 3584
  intermediate_size: 18944
  vocabulary_size: 152064
  activation_function: SwiGLU
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 1000000
  kv_cache_rating: low
  key_detail: 28 query heads share 4 KV heads; the QKV projections carry bias; the 128K context uses YaRN on a config set for 32,768 tokens
filters:
  size_category: small
  is_quantized: false
  quant_format: original
  is_moe: false
  pipeline_tag: text-generation
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
quotes:
  summary: This repo contains the instruction-tuned 7B Qwen2.5 model
  what_sets_it_apart: Significantly **more knowledge** and has greatly improved capabilities in **coding** and **mathematics**, thanks to our specialized expert models in these domains.
  running_it: The current `config.json` is set for context length up to 32,768 tokens.
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
- qwen
- alibaba
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
  title: 'YaRN: Efficient Context Window Extension of Large Language Models'
  trust: 0.95
- url: https://arxiv.org/abs/2407.10671
  title: Qwen2 Technical Report
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen2/modeling_qwen2.py
  title: 'transformers/models/qwen2/modeling_qwen2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
related:
- architecture-era/transformer
- license/apache-2-0
---

## Notes

The base `config.json` reports `max_position_embeddings: 32768`; the 128K context needs YaRN
scaling (set in `rope_scaling`). The tokenizer vocabulary is 152064 tokens. See
[[license/apache-2-0]] for licensing and [[architecture-era/transformer]] for the architecture lineage.
