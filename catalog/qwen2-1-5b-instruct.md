---
id: model/qwen2-1-5b-instruct
type: model
slug: qwen2-1-5b-instruct
schema_version: 3.0
title: Qwen2-1.5B-Instruct
organization: organization/qwen
release_date: '2024-06-03'
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
parameters_active_b: 1.54
total_parameters: 1543714304
context_window_tokens: 32768
summary: Qwen2-1.5B-Instruct is a language model from Qwen that reads text and writes text in reply. It is the instruction-tuned 1.5-billion-parameter model of the Qwen2 series, which means a base model was trained further to follow instructions and hold a conversation. The card says Qwen2 ranges from 0.5 to 72 billion parameters, including a Mixture-of-Experts model, and its metadata lists Qwen2.5-1.5B-Instruct as the newer version.
what_sets_it_apart: The card says Qwen2 is based on the Transformer architecture with SwiGLU activation, attention QKV bias and grouped-query attention, and has an improved tokenizer adapted to many natural languages and to code. It says the models were post-trained with both supervised fine-tuning and direct preference optimization.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 12
  kv_heads: 2
  head_size: 128
  hidden_size: 1536
  intermediate_size: 8960
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 71054
  likes: 176
  last_modified: '2026-11-20'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2-1.5B-Instruct
aliases:
- Qwen/Qwen2-1.5B-Instruct
- qwen2-1.5b-instruct
lineage:
  parents: []
  children: []
quotes:
  summary: This repo contains the instruction-tuned 1.5B Qwen2 model.
  what_sets_it_apart: It is based on the Transformer architecture with SwiGLU activation, attention QKV bias, group query attention, etc.
tags:
- qwen2
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen2-1.5B-Instruct
  title: Qwen/Qwen2-1.5B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2-1.5B-Instruct/blob/main/config.json
  title: Qwen/Qwen2-1.5B-Instruct — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen2/modeling_qwen2.py
  title: 'transformers/models/qwen2/modeling_qwen2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


