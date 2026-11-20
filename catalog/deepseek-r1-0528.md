---
id: model/deepseek-r1-0528
type: model
slug: deepseek-r1-0528
schema_version: 1.1
title: DeepSeek-R1-0528
organization: organization/deepseek
release_date: '2025-05-28'
release_year: 2025
release_month: 5
license: license/mit
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
total_parameters: 684531386000
context_window_tokens: 163840
summary: DeepSeek-R1-0528 is a language model released by DeepSeek on May 28, 2025, with 685 billion parameters and a 163840 token context window. It is a mixture of experts model that improves its depth of reasoning and inference capabilities through increased computational resources and algorithmic optimization mechanisms during post-training.
why_it_matters: The DeepSeek-R1 series supports commercial use and distillation under the MIT License.
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
  downloads_30d: 140009
  likes: 2496
  last_modified: '2025-05-29'
  observed_at: '2026-11-19'
  source: https://huggingface.co/deepseek-ai/DeepSeek-R1-0528
aliases:
- deepseek-ai/DeepSeek-R1-0528
lineage:
  parents: []
  children: []
quote: DeepSeek-R1 series (including Base and Chat) supports commercial use and distillation.
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
- url: https://huggingface.co/deepseek-ai/DeepSeek-R1-0528
  title: deepseek-ai/DeepSeek-R1-0528 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-R1-0528/blob/main/config.json
  title: deepseek-ai/DeepSeek-R1-0528 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2501.12948
  title: arXiv:2501.12948
  trust: 0.95
---

## Summary

DeepSeek-R1-0528 is a language model released by DeepSeek on May 28, 2025, with 685 billion parameters and a 163840 token context window. It is a mixture of experts model that improves its depth of reasoning and inference capabilities through increased computational resources and algorithmic optimization mechanisms during post-training.

## Why It Matters

The DeepSeek-R1 series supports commercial use and distillation under the MIT License.
