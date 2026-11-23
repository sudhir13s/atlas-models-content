---
id: model/qwen3-coder-next-fp8
type: model
slug: qwen3-coder-next-fp8
schema_version: 1.1
title: Qwen3-Coder-Next-FP8
organization: organization/qwen
release_date: '2026-02-01'
release_year: 2026
release_month: 2
license: license/apache-2-0
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
- task/code-models
- task/tool-calling-models
total_parameters: 79674391296
context_window_tokens: 262144
summary: Qwen3-Coder-Next-FP8 is an FP8-quantized copy of Qwen3-Coder-Next, an open-weight language model from Qwen for coding agents and local development. FP8 means the weights are stored as 8-bit floating-point numbers to shrink the model; the card names the method as fine-grained FP8 with a block size of 128. The card lists 80B parameters in total with 3B activated, and a native context length of 262,144 tokens.
intended_use: The card says the model is designed specifically for coding agents and local development. It says the 256k context and adaptability to different scaffold templates let it work with various command-line and IDE platforms.
what_sets_it_apart: Qwen claims that, with only 3B of its 80B parameters activated, it reaches performance comparable to models with 10 to 20 times more active parameters. The layout alternates Gated DeltaNet and Gated Attention layers, each followed by a mixture-of-experts layer with 512 experts, 10 of them activated plus 1 shared. The card says it supports only non-thinking mode, and that the results in the card come from the original bfloat16 model before FP8 quantization.
running_it: The card advises using the latest Transformers library, or the latest SGLang or vLLM for an OpenAI-compatible endpoint. It says that if memory runs out, the context length can be reduced, for example to 32,768 tokens. The quantization settings are in the quantization_config field of config.json.
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 16
  kv_heads: 2
  head_size: 256
  hidden_size: 2048
  intermediate_size: 5120
  context_window_tokens: 262144
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 5000000.0
moe_config:
  expert_count: 512
  active_experts: 10
  shared_expert: true
filters:
  size_category: xlarge
  is_quantized: true
  quant_format: fp8
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 110119
  likes: 212
  last_modified: '2026-02-03'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-Coder-Next-FP8
aliases:
- Qwen/Qwen3-Coder-Next-FP8
lineage:
  parents: []
  children: []
quotes:
  summary: This repository contains the **FP8-quantized Qwen3-Coder-Next** model checkpoint for convenience and performance.
  intended_use: an open-weight language model designed specifically for coding agents and local development
  what_sets_it_apart: With only 3B activated parameters (80B total parameters), it achieves performance comparable to models with 10–20x more active parameters
  running_it: If you encounter out-of-memory (OOM) issues, consider reducing the context length to a shorter value, such as `32,768`.
tags:
- llm
- text-generation
- qwen3-next
- moe
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-Coder-Next-FP8
  title: Qwen/Qwen3-Coder-Next-FP8 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-Coder-Next-FP8/blob/main/config.json
  title: Qwen/Qwen3-Coder-Next-FP8 — config.json
  trust: 0.9
---

## Summary

Qwen3-Coder-Next-FP8 is an FP8-quantized copy of Qwen3-Coder-Next, an open-weight language model from Qwen for coding agents and local development. FP8 means the weights are stored as 8-bit floating-point numbers to shrink the model; the card names the method as fine-grained FP8 with a block size of 128. The card lists 80B parameters in total with 3B activated, and a native context length of 262,144 tokens.

## Intended Use

The card says the model is designed specifically for coding agents and local development. It says the 256k context and adaptability to different scaffold templates let it work with various command-line and IDE platforms.

## What Sets It Apart

Qwen claims that, with only 3B of its 80B parameters activated, it reaches performance comparable to models with 10 to 20 times more active parameters. The layout alternates Gated DeltaNet and Gated Attention layers, each followed by a mixture-of-experts layer with 512 experts, 10 of them activated plus 1 shared. The card says it supports only non-thinking mode, and that the results in the card come from the original bfloat16 model before FP8 quantization.

## Running It

The card advises using the latest Transformers library, or the latest SGLang or vLLM for an OpenAI-compatible endpoint. It says that if memory runs out, the context length can be reduced, for example to 32,768 tokens. The quantization settings are in the quantization_config field of config.json.
