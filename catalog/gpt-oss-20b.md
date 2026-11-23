---
id: model/gpt-oss-20b
type: model
slug: gpt-oss-20b
schema_version: 1.1
title: gpt-oss-20b
organization: organization/openai
release_date: '2025-08-04'
release_year: 2025
release_month: 8
license: license/apache-2-0
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
- task/reasoning-models
- task/tool-calling-models
total_parameters: 20914757184
context_window_tokens: 131072
summary: gpt-oss-20b is an open-weight language model from OpenAI, meaning its trained weights can be downloaded and run locally. It is the smaller of two gpt-oss models and has 21 billion parameters, of which 3.6 billion are active for each token. The card describes the series as designed for reasoning, agentic tasks (where a model calls tools) and general developer use.
intended_use: OpenAI recommends gpt-oss-20b for lower-latency, local or specialized use cases, and its larger sibling gpt-oss-120b for production, general-purpose and high-reasoning use. The card says both models should only be used with the harmony response format, because they will not work correctly otherwise.
what_sets_it_apart: The card highlights an adjustable reasoning effort (low, medium or high) and access to the model's full chain of thought, which it says is not meant to be shown to end users. It also lists built-in function calling, web browsing, Python code execution and structured outputs. The MoE (mixture-of-experts) weights were post-trained with MXFP4 quantization, a compact 4-bit number format, so the model runs within 16GB of memory according to the card.
running_it: The card says the model must be used with OpenAI's harmony response format. The Transformers chat template applies it automatically, but anyone calling model.generate directly has to apply the format by hand or use the openai-harmony package.
fingerprint:
  attention_type: GQA
  num_layers: 24
  attention_heads: 64
  kv_heads: 8
  head_size: 64
  hidden_size: 2880
  intermediate_size: 2880
  context_window_tokens: 131072
  vocabulary_size: 201088
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 150000.0
moe_config:
  expert_count: 32
  active_experts: 4
filters:
  size_category: large
  is_quantized: true
  quant_format: mxfp4
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 5560822
  likes: 5330
  last_modified: '2025-08-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/openai/gpt-oss-20b
aliases:
- openai/gpt-oss-20b
lineage:
  parents: []
  children: []
quotes:
  summary: '`gpt-oss-20b` — for lower latency, and local or specialized use cases (21B parameters with 3.6B active parameters)'
  intended_use: Both models were trained on our [harmony response format](https://github.com/openai/harmony) and should only be used with the harmony format as it will not work correctly otherwise.
  what_sets_it_apart: Easily adjust the reasoning effort (low, medium, high) based on your specific use case and latency needs.
  running_it: If you use the Transformers chat template, it will automatically apply the [harmony response format](https://github.com/openai/harmony).
tags:
- llm
- text-generation
- gpt-oss
- moe
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/openai/gpt-oss-20b
  title: openai/gpt-oss-20b — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai/gpt-oss-20b/blob/main/config.json
  title: openai/gpt-oss-20b — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2508.10925
  title: arXiv:2508.10925
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gpt_oss/modeling_gpt_oss.py
  title: 'transformers/models/gpt_oss/modeling_gpt_oss.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

gpt-oss-20b is an open-weight language model from OpenAI, meaning its trained weights can be downloaded and run locally. It is the smaller of two gpt-oss models and has 21 billion parameters, of which 3.6 billion are active for each token. The card describes the series as designed for reasoning, agentic tasks (where a model calls tools) and general developer use.

## Intended Use

OpenAI recommends gpt-oss-20b for lower-latency, local or specialized use cases, and its larger sibling gpt-oss-120b for production, general-purpose and high-reasoning use. The card says both models should only be used with the harmony response format, because they will not work correctly otherwise.

## What Sets It Apart

The card highlights an adjustable reasoning effort (low, medium or high) and access to the model's full chain of thought, which it says is not meant to be shown to end users. It also lists built-in function calling, web browsing, Python code execution and structured outputs. The MoE (mixture-of-experts) weights were post-trained with MXFP4 quantization, a compact 4-bit number format, so the model runs within 16GB of memory according to the card.

## Running It

The card says the model must be used with OpenAI's harmony response format. The Transformers chat template applies it automatically, but anyone calling model.generate directly has to apply the format by hand or use the openai-harmony package.
