---
id: model/deepseek-v3-2
type: model
slug: deepseek-v3-2
schema_version: 1.1
title: DeepSeek-V3.2
organization: organization/deepseek
release_date: '2025-12-01'
release_year: 2025
release_month: 12
license: license/mit
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
total_parameters: 685355329792
context_window_tokens: 163840
summary: DeepSeek-V3.2 is a 685 billion parameter language model from DeepSeek released under the MIT License, featuring a 163840 token context window, a mixture of experts design, and DeepSeek Sparse Attention for computational efficiency in long-context scenarios.
why_it_matters: By implementing a robust reinforcement learning protocol and scaling post-training compute, it performs comparably to GPT-5, while its high-compute variant DeepSeek-V3.2-Speciale surpasses GPT-5 and achieves reasoning proficiency on par with Gemini-3.0-Pro.
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
  downloads_30d: 9854997
  likes: 1605
  last_modified: '2025-12-01'
  observed_at: '2026-11-19'
  source: https://huggingface.co/deepseek-ai/DeepSeek-V3.2
aliases:
- deepseek-ai/DeepSeek-V3.2
- deepseek-v3.2
lineage:
  parents: []
  children: []
quote: By implementing a robust RL protocol and scaling post-training compute, *DeepSeek-V3.2* performs comparably to GPT-5.
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
- deepseek-v32
- moe
- mla
updated: '2026-11-20'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-V3.2
  title: deepseek-ai/DeepSeek-V3.2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-V3.2/blob/main/config.json
  title: deepseek-ai/DeepSeek-V3.2 — config.json
  trust: 0.9
---

## Summary

DeepSeek-V3.2 is a 685 billion parameter language model from DeepSeek released under the MIT License, featuring a 163840 token context window, a mixture of experts design, and DeepSeek Sparse Attention for computational efficiency in long-context scenarios.

## Why It Matters

By implementing a robust reinforcement learning protocol and scaling post-training compute, it performs comparably to GPT-5, while its high-compute variant DeepSeek-V3.2-Speciale surpasses GPT-5 and achieves reasoning proficiency on par with Gemini-3.0-Pro.
