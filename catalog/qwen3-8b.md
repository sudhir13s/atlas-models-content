---
id: model/qwen3-8b
type: model
slug: qwen3-8b
schema_version: 3.0
title: Qwen3-8B
organization: organization/qwen
release_date: '2025-04-27'
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
- task/tool-calling-models
parameters_active_b: 8.19
total_parameters: 8190735360
context_window_tokens: 32768
summary: Qwen3-8B is a language model from Qwen that reads text and writes text in reply. The card lists 8.2B parameters and 36 layers, and says it supports a context of 32,768 tokens natively and 131,072 tokens with YaRN. It is part of the Qwen3 series, which the card describes as a suite of dense and mixture-of-experts models.
what_sets_it_apart: Qwen states that a single model can switch between a thinking mode, for complex logical reasoning, math and coding, and a non-thinking mode for efficient general dialogue. The card also lists improved reasoning, human preference alignment, agent abilities that connect to external tools, and support for more than 100 languages and dialects.
running_it: For thinking mode the card recommends temperature 0.6, top-p 0.95, top-k 20 and min-p 0, and says not to use greedy decoding because it can cause endless repetition. For non-thinking mode it suggests temperature 0.7 and top-p 0.8. It advises enabling YaRN only for longer inputs because static YaRN may affect performance on shorter texts.
fingerprint:
  attention_type: GQA
  num_layers: 36
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 12288
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 6141295
  likes: 2204
  last_modified: '2025-07-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-8B
gguf_repositories:
  repositories:
  - publisher: Qwen
    repo_id: Qwen/Qwen3-8B-GGUF
    address: https://huggingface.co/Qwen/Qwen3-8B-GGUF
  - publisher: unsloth
    repo_id: unsloth/Qwen3-8B-GGUF
    address: https://huggingface.co/unsloth/Qwen3-8B-GGUF
  observed_at: '2026-11-23'
  source: https://huggingface.co/models?library=gguf&other=base_model:quantized:Qwen/Qwen3-8B
variants:
- name: Qwen/Qwen3-8B-AWQ
  format: awq
  address: https://huggingface.co/Qwen/Qwen3-8B-AWQ
  publisher: Qwen
- name: unsloth/Qwen3-8B-unsloth-bnb-4bit
  format: bnb-4bit
  address: https://huggingface.co/unsloth/Qwen3-8B-unsloth-bnb-4bit
  publisher: unsloth
aliases:
- Qwen/Qwen3-8B
- qwen3-8b-awq
- qwen3-8b-unsloth-bnb-4bit
lineage:
  parents:
  - model/qwen3-8b-base
  children: []
quotes:
  summary: offering a comprehensive suite of dense and mixture-of-experts (MoE) models
  what_sets_it_apart: Uniquely support of seamless switching between thinking mode
  running_it: For thinking mode (`enable_thinking=True`), use `Temperature=0.6`, `TopP=0.95`, `TopK=20`, and `MinP=0`.
locked:
- context_window_tokens
tags:
- qwen3
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-8B
  title: Qwen/Qwen3-8B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
  title: Qwen/Qwen3-8B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2309.00071
  title: arXiv:2309.00071
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'transformers/models/qwen3/modeling_qwen3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


