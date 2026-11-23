---
id: model/kimi-k2-5
type: model
slug: kimi-k2-5
schema_version: 1.1
title: Kimi-K2.5
organization: organization/moonshot-ai
release_date: '2026-01-01'
release_year: 2026
release_month: 1
license_note: modified-mit
model_category: multimodal
tasks:
- task/vision-language-models
total_parameters: 1026879376368
context_window_tokens: 262144
summary: Kimi K2.5 is an open-source model from moonshotai that takes both images and text and is built for agent work, where a model calls tools and carries out multi-step tasks. The card says it was built by continued pretraining on about 15 trillion mixed visual and text tokens on top of Kimi-K2-Base. It lists a Mixture-of-Experts design with 1T total parameters, 32B activated per token, and a 256K context length.
intended_use: 'The card describes instant and thinking modes and both conversational and agentic use. One highlighted use is coding with vision: generating code from visual specifications such as UI designs and video workflows, and orchestrating tools for visual data processing. The card says chat with video content is experimental and available only through the official API.'
what_sets_it_apart: The card describes native multimodality, meaning the model was pretrained on vision and language tokens together rather than having vision added afterward. It also describes an Agent Swarm scheme, in which the model splits a complex task into parallel sub-tasks run by agents it creates on the fly, each specialized for its domain. Its weights use native INT4 quantization, the same method as Kimi-K2-Thinking.
running_it: For third-party serving with vLLM or SGLang, the card recommends a temperature of 1.0 for Thinking mode and 0.6 for Instant mode, with top_p of 0.95.
fingerprint:
  attention_type: MLA
  num_layers: 61
  attention_heads: 64
  kv_heads: 64
  head_size: 112
  hidden_size: 7168
  intermediate_size: 18432
  context_window_tokens: 262144
  vocabulary_size: 163840
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 50000.0
moe_config:
  expert_count: 384
  active_experts: 8
  shared_expert: true
filters:
  size_category: xlarge
  is_quantized: false
  is_moe: true
  attention_type: MLA
  is_gated: false
  pipeline_tag: image-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 392853
  likes: 2902
  last_modified: '2026-11-20'
  observed_at: '2026-11-20'
  source: https://huggingface.co/moonshotai/Kimi-K2.5
variants:
- name: nvidia/Kimi-K2.5-NVFP4
  format: nvfp4
  address: https://huggingface.co/nvidia/Kimi-K2.5-NVFP4
  publisher: nvidia
aliases:
- moonshotai/Kimi-K2.5
- kimi-k2.5-nvfp4
lineage:
  parents: []
  children: []
quotes:
  summary: built through continual pretraining on approximately 15 trillion mixed visual and text tokens atop Kimi-K2-Base
  intended_use: K2.5 generates code from visual specifications (UI designs, video workflows)
  what_sets_it_apart: It decomposes complex tasks into parallel sub-tasks executed by dynamically instantiated, domain-specific agents.
  running_it: The recommended `temperature` will be `1.0` for Thinking mode and `0.6` for Instant mode.
tags:
- llm
- image-text-to-text
- kimi-k25
- moe
- mla
updated: '2026-11-23'
sources:
- url: https://huggingface.co/moonshotai/Kimi-K2.5
  title: moonshotai/Kimi-K2.5 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/moonshotai/Kimi-K2.5/blob/main/config.json
  title: moonshotai/Kimi-K2.5 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2602.02276
  title: arXiv:2602.02276
  trust: 0.95
---

## Summary

Kimi K2.5 is an open-source model from moonshotai that takes both images and text and is built for agent work, where a model calls tools and carries out multi-step tasks. The card says it was built by continued pretraining on about 15 trillion mixed visual and text tokens on top of Kimi-K2-Base. It lists a Mixture-of-Experts design with 1T total parameters, 32B activated per token, and a 256K context length.

## Intended Use

The card describes instant and thinking modes and both conversational and agentic use. One highlighted use is coding with vision: generating code from visual specifications such as UI designs and video workflows, and orchestrating tools for visual data processing. The card says chat with video content is experimental and available only through the official API.

## What Sets It Apart

The card describes native multimodality, meaning the model was pretrained on vision and language tokens together rather than having vision added afterward. It also describes an Agent Swarm scheme, in which the model splits a complex task into parallel sub-tasks run by agents it creates on the fly, each specialized for its domain. Its weights use native INT4 quantization, the same method as Kimi-K2-Thinking.

## Running It

For third-party serving with vLLM or SGLang, the card recommends a temperature of 1.0 for Thinking mode and 0.6 for Instant mode, with top_p of 0.95.
