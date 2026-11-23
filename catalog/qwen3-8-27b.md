---
id: model/qwen3-8-27b
type: model
slug: qwen3-8-27b
schema_version: 1.1
title: Qwen3.8-27B
organization: organization/qwen
release_date: '2026-08-05'
release_year: 2026
release_month: 8
license: license/apache-2-0
model_category: language
tasks:
- task/chat-models
- task/vision-language-models
parameters_active_b: 27.78
total_parameters: 27781427952
context_window_tokens: 262144
summary: Qwen3.8-27B is a 27-billion-parameter language model from Qwen that also has a vision encoder, so it reads text, images and video and writes text in reply. The card describes it as a dense model, with every parameter in use for each token, that comes after the Qwen3.5 and Qwen3.6 series. It has a context length of 262,144 tokens natively, extensible up to 1,000,000 tokens.
intended_use: Qwen presents it for coding, professional work, research and long-horizon agentic tasks, where a model carries out many steps on its own. It describes the model as designed to carry complex, multi-step tasks through to completion.
what_sets_it_apart: The card lists a layer layout that alternates three Gated DeltaNet layers (a linear-attention design) with one Gated Attention layer, repeated 16 times. Thinking mode is on by default and can be turned off per request, and the reasoning_effort setting tunes how deeply the model reasons.
running_it: The card suggests temperature=1.0, top_p=0.95 and top_k=20 in thinking mode, and temperature=0.7, top_p=0.80 and top_k=20 in instruct (non-thinking) mode. For production or high-throughput work it recommends the SGLang, vLLM or TokenSpeed serving engines.
fingerprint:
  attention_type: GQA
  num_layers: 64
  attention_heads: 24
  kv_heads: 4
  head_size: 256
  hidden_size: 5120
  intermediate_size: 17408
  context_window_tokens: 262144
  vocabulary_size: 248320
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000000.0
filters:
  size_category: large
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: image-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 6454419
  likes: 20815
  last_modified: '2026-11-07'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Qwen/Qwen3.8-27B
aliases:
- Qwen/Qwen3.8-27B
lineage:
  parents: []
  children: []
quotes:
  summary: 'Type: Causal Language Model with Vision Encoder'
  intended_use: designed to carry complex, multi-step tasks through to completion with greater reliability.
  what_sets_it_apart: Thinking mode is on by default and can be disabled per request
  running_it: For production workloads or high-throughput scenarios, dedicated serving engines such as SGLang, vLLM, or TokenSpeed are recommended.
tags:
- llm
- image-text-to-text
- qwen3-5
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3.8-27B
  title: Qwen/Qwen3.8-27B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3.8-27B/blob/main/config.json
  title: Qwen/Qwen3.8-27B — config.json
  trust: 0.9
---

## Summary

Qwen3.8-27B is a 27-billion-parameter language model from Qwen that also has a vision encoder, so it reads text, images and video and writes text in reply. The card describes it as a dense model, with every parameter in use for each token, that comes after the Qwen3.5 and Qwen3.6 series. It has a context length of 262,144 tokens natively, extensible up to 1,000,000 tokens.

## Intended Use

Qwen presents it for coding, professional work, research and long-horizon agentic tasks, where a model carries out many steps on its own. It describes the model as designed to carry complex, multi-step tasks through to completion.

## What Sets It Apart

The card lists a layer layout that alternates three Gated DeltaNet layers (a linear-attention design) with one Gated Attention layer, repeated 16 times. Thinking mode is on by default and can be turned off per request, and the reasoning_effort setting tunes how deeply the model reasons.

## Running It

The card suggests temperature=1.0, top_p=0.95 and top_k=20 in thinking mode, and temperature=0.7, top_p=0.80 and top_k=20 in instruct (non-thinking) mode. For production or high-throughput work it recommends the SGLang, vLLM or TokenSpeed serving engines.
