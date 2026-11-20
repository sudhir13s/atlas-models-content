---
id: model/deepseek-coder-6-7b-instruct
type: model
slug: deepseek-coder-6-7b-instruct
schema_version: 1.1
title: deepseek-coder-6.7b-instruct
organization: organization/deepseek
release_date: '2023-10-29'
release_year: 2023
release_month: 10
license: license/deepseek
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 6.74
total_parameters: 6740512768
context_window_tokens: 16384
summary: deepseek-coder-6.7b-instruct is a 6.7B parameter model initialized from deepseek-coder-6.7b-base and fine-tuned on 2B tokens of instruction data by DeepSeek.
fingerprint:
  attention_type: MHA
  num_layers: 32
  attention_heads: 32
  kv_heads: 32
  head_size: 128
  hidden_size: 4096
  intermediate_size: 11008
  context_window_tokens: 16384
  vocabulary_size: 32256
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 100000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 38286
  likes: 540
  last_modified: '2024-02-02'
  observed_at: '2026-11-19'
  source: https://huggingface.co/deepseek-ai/deepseek-coder-6.7b-instruct
aliases:
- deepseek-ai/deepseek-coder-6.7b-instruct
- deepseek-coder-6.7b-instruct
lineage:
  parents: []
  children: []
quotes:
  summary: deepseek-coder-6.7b-instruct is a 6.7B parameter model initialized from deepseek-coder-6.7b-base and fine-tuned on 2B tokens of instruction data.
review:
  score: 10.0
  reviewed_by:
    provider: nvidia-gemma-4-31b
    model: nvidia_nim/google/gemma-4-31b-it
  prompt_versions:
    writer: 2.0.0
    critic: 1.2.0
generated_by:
  job: backfill
  run_id: 7
  provider: gemini-flash-lite
  model: gemini/gemini-3.5-flash-lite
  prompt_version: 2.0.0
tags:
- llm
- text-generation
- llama
- dense
- mha
updated: '2026-11-20'
sources:
- url: https://huggingface.co/deepseek-ai/deepseek-coder-6.7b-instruct
  title: deepseek-ai/deepseek-coder-6.7b-instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/deepseek-coder-6.7b-instruct/blob/main/config.json
  title: deepseek-ai/deepseek-coder-6.7b-instruct — config.json
  trust: 0.9
---

## Summary

deepseek-coder-6.7b-instruct is a 6.7B parameter model initialized from deepseek-coder-6.7b-base and fine-tuned on 2B tokens of instruction data by DeepSeek.
