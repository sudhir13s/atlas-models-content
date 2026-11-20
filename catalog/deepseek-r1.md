---
id: model/deepseek-r1
type: model
slug: deepseek-r1
schema_version: 1.1
title: DeepSeek-R1
organization: organization/deepseek
release_date: '2025-01-20'
release_year: 2025
release_month: 1
license: license/mit
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
total_parameters: 684489845504
context_window_tokens: 163840
summary: DeepSeek-R1 is a first-generation reasoning model from DeepSeek that incorporates cold-start data before reinforcement learning to address issues like endless repetition and poor readability. It achieves performance comparable to OpenAI-o1 across math, code, and reasoning tasks.
why_it_matters: DeepSeek-R1 achieves performance comparable to OpenAI-o1 across math, code, and reasoning tasks, and its open-source release supports the research community.
fingerprint:
  attention_type: MLA
  num_layers: 61
  attention_heads: 128
  kv_heads: 128
  head_size: 56
  hidden_size: 7168
  intermediate_size: 18432
  context_window_tokens: 163840
  vocabulary_size: 129280
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
moe_config:
  expert_count: 256
  active_experts: 8
  shared_expert: true
filters:
  size_category: xlarge
  is_quantized: true
  quant_format: fp8
  is_moe: true
  attention_type: MLA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 1662310
  likes: 14467
  last_modified: '2025-03-27'
  observed_at: '2026-11-19'
  source: https://huggingface.co/deepseek-ai/DeepSeek-R1
aliases:
- deepseek-ai/DeepSeek-R1
lineage:
  parents: []
  children: []
quote: DeepSeek-R1 achieves performance comparable to OpenAI-o1 across math, code, and reasoning tasks.
review:
  score: 10.0
  reviewed_by:
    provider: nvidia-gemma-4-31b
    model: nvidia_nim/google/gemma-4-31b-it
  prompt_versions:
    writer: 1.0.0
    critic: 1.2.0
generated_by:
  job: backfill
  run_id: 6
  provider: gemini-flash-lite
  model: gemini/gemini-3.5-flash-lite
  prompt_version: 1.0.0
tags:
- llm
- text-generation
- deepseek-v3
- moe
- mla
updated: '2026-11-20'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-R1
  title: deepseek-ai/DeepSeek-R1 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-R1/blob/main/config.json
  title: deepseek-ai/DeepSeek-R1 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2501.12948
  title: arXiv:2501.12948
  trust: 0.95
---

## Summary

DeepSeek-R1 is a first-generation reasoning model from DeepSeek that incorporates cold-start data before reinforcement learning to address issues like endless repetition and poor readability. It achieves performance comparable to OpenAI-o1 across math, code, and reasoning tasks.

## Why It Matters

DeepSeek-R1 achieves performance comparable to OpenAI-o1 across math, code, and reasoning tasks, and its open-source release supports the research community.
