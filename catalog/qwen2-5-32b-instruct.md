---
id: model/qwen2-5-32b-instruct
type: model
slug: qwen2-5-32b-instruct
schema_version: 3.0
title: Qwen2.5-32B-Instruct
organization: organization/qwen
release_date: '2024-09-17'
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
parameters_active_b: 32.76
total_parameters: 32763876352
context_window_tokens: 32768
base_models:
- name: Qwen/Qwen2.5-32B
  address: https://huggingface.co/Qwen/Qwen2.5-32B
summary: Qwen2.5 32B Instruct is a language model from Qwen that reads text and writes text in reply. It is the instruction-tuned member of the Qwen2.5 series at the 32B size, which means the base model was trained further to follow instructions and hold a conversation. The card lists 32.5B parameters and 64 layers, and says the Qwen2.5 series ranges from 0.5 to 72 billion parameters.
what_sets_it_apart: Qwen states that, compared with the earlier Qwen2 series, Qwen2.5 has more knowledge and improved coding and mathematics, follows instructions better, generates long texts of over 8K tokens, understands structured data such as tables and produces structured outputs such as JSON. For this size the card lists a full context of 131,072 tokens with generation up to 8192 tokens, and support for over 29 languages.
running_it: The model card says the published config.json is set for a context of up to 32,768 tokens, and that longer inputs rely on YaRN, a technique for extending the length a model handles. The card advises enabling it only when long contexts are needed, because vLLM supports only static YaRN, which may affect performance on shorter texts.
fingerprint:
  attention_type: GQA
  num_layers: 64
  attention_heads: 40
  kv_heads: 8
  head_size: 128
  hidden_size: 5120
  intermediate_size: 27648
  vocabulary_size: 152064
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: large
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 664543
  likes: 378
  last_modified: '2024-09-25'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2.5-32B-Instruct
variants:
- name: Qwen/Qwen2.5-32B-Instruct-AWQ
  format: awq
  address: https://huggingface.co/Qwen/Qwen2.5-32B-Instruct-AWQ
  publisher: Qwen
aliases:
- Qwen/Qwen2.5-32B-Instruct
- qwen2.5-32b-instruct
- qwen2.5-32b-instruct-awq
lineage:
  parents: []
  children: []
quotes:
  summary: This repo contains the instruction-tuned 32B Qwen2.5 model
  what_sets_it_apart: Significantly **more knowledge** and has greatly improved capabilities in **coding** and **mathematics**, thanks to our specialized expert models in these domains.
  running_it: The current `config.json` is set for context length up to 32,768 tokens.
tags:
- qwen2
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-32B-Instruct
  title: Qwen/Qwen2.5-32B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-32B-Instruct/blob/main/config.json
  title: Qwen/Qwen2.5-32B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2309.00071
  title: arXiv:2309.00071
  trust: 0.95
- url: https://arxiv.org/abs/2407.10671
  title: arXiv:2407.10671
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen2/modeling_qwen2.py
  title: 'transformers/models/qwen2/modeling_qwen2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


