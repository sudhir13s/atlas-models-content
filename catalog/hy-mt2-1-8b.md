---
id: model/hy-mt2-1-8b
type: model
slug: hy-mt2-1-8b
schema_version: 3.0
title: Hy-MT2-1.8B
organization: organization/tencent
release_date: '2026-05-11'
license: license/apache-2-0
model_category: language
model_kind: transformer-language
tasks:
- task/translation
parameters_active_b: 2.04
total_parameters: 2038515712
context_window_tokens: 262144
summary: Hy-MT2-1.8B is the 1.8-billion-parameter member of Hy-MT2, a family of machine translation models from Tencent Hy, which translate text from one language to another. The card says all sizes in the family support translation among 33 languages and follow translation instructions given in several languages.
intended_use: The card presents the model for translation in complex real-world settings and shows instruction templates for default translation, translation with a terminology list, a requested style, user preferences, preserved delimiters, structured data such as JSON, and background information. It asks that language names in prompts be written in full.
what_sets_it_apart: The card describes the family as fast-thinking and as able to follow translation instructions. Tencent also released IFMTBench, a benchmark for translation instruction following. For phones and similar devices, the card says a 1.25-bit quantized build made with AngelSlim reduces the 1.8B model's storage to 440 MB.
running_it: For the 1.8B and 7B sizes the card recommends a temperature of 0.7, top-p of 0.6, top-k of 20, a repetition penalty of 1.05 and a maximum of 4096 new tokens. It notes that the models have no default system prompt and that the Transformers library version 5.6.0 or newer is needed.
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 16
  kv_heads: 4
  head_size: 128
  hidden_size: 2048
  intermediate_size: 6144
  vocabulary_size: 120818
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: translation
  library: transformers
hub_signals:
  downloads_30d: 21419
  likes: 1294
  last_modified: '2026-05-26'
  observed_at: '2026-11-21'
  source: https://huggingface.co/tencent/Hy-MT2-1.8B
aliases:
- tencent/Hy-MT2-1.8B
lineage:
  parents: []
  children: []
quotes:
  summary: Hy-MT2 is a family of “fast-thinking” multilingual translation models designed for complex real-world scenarios.
  intended_use: Hy-MT2 Translation Task Instruction Examples
  what_sets_it_apart: all of which support translation among 33 languages and effectively follow translation instructions in multiple languages.
  running_it: Note that our models do not have a default system_prompt.
tags:
- hunyuan-v1-dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/tencent/Hy-MT2-1.8B
  title: tencent/Hy-MT2-1.8B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/tencent/Hy-MT2-1.8B/blob/main/config.json
  title: tencent/Hy-MT2-1.8B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2605.22064
  title: arXiv:2605.22064
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/hunyuan_v1_dense/modeling_hunyuan_v1_dense.py
  title: 'transformers/models/hunyuan_v1_dense/modeling_hunyuan_v1_dense.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


