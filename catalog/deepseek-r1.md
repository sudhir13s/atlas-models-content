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
- task/reasoning-models
total_parameters: 684489845504
context_window_tokens: 131072
summary: DeepSeek-R1 is a reasoning language model from DeepSeek that works through a problem in a long chain of thought before it answers. The card says it was trained from DeepSeek-V3-Base with large-scale reinforcement learning, which improves a model by rewarding good results, with cold-start data added before the reinforcement learning. It is a mixture-of-experts model with 671 billion total parameters, 37 billion active per token, and a 128K context length.
what_sets_it_apart: DeepSeek describes a training pipeline with two reinforcement-learning stages, to find better reasoning patterns and to match human preferences, and two supervised fine-tuning stages that seed the model's reasoning and non-reasoning abilities. The card says its predecessor DeepSeek-R1-Zero was trained with reinforcement learning alone, without a supervised first step, and showed endless repetition, poor readability and language mixing, which the cold-start data in DeepSeek-R1 addresses.
running_it: The card recommends a temperature between 0.5 and 0.7, with 0.6 suggested, to prevent endless repetition, and advises against a system prompt, with all instructions placed in the user prompt. It also suggests forcing the output to begin with the text <think> so that the model reasons before answering.
fingerprint:
  attention_type: MLA
  num_layers: 61
  attention_heads: 128
  kv_heads: 128
  head_size: 56
  hidden_size: 7168
  intermediate_size: 18432
  context_window_tokens: 131072
  vocabulary_size: 129280
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
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
  downloads_30d: 1634572
  likes: 14470
  last_modified: '2025-03-27'
  observed_at: '2026-11-20'
  source: https://huggingface.co/deepseek-ai/DeepSeek-R1
aliases:
- deepseek-ai/DeepSeek-R1
lineage:
  parents: []
  children: []
quotes:
  summary: We introduce our first-generation reasoning models, DeepSeek-R1-Zero and DeepSeek-R1.
  what_sets_it_apart: The pipeline incorporates two RL stages aimed at discovering improved reasoning patterns and aligning with human preferences, as well as two SFT stages that serve as the seed for the model's reasoning and non-reasoning capabilities.
  running_it: Set the temperature within the range of 0.5-0.7 (0.6 is recommended) to prevent endless repetitions or incoherent outputs.
locked:
- context_window_tokens
- fingerprint.context_window_tokens
tags:
- llm
- text-generation
- deepseek-v3
- moe
- mla
updated: '2026-11-23'
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
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/deepseek_v3/modeling_deepseek_v3.py
  title: 'transformers/models/deepseek_v3/modeling_deepseek_v3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

DeepSeek-R1 is a reasoning language model from DeepSeek that works through a problem in a long chain of thought before it answers. The card says it was trained from DeepSeek-V3-Base with large-scale reinforcement learning, which improves a model by rewarding good results, with cold-start data added before the reinforcement learning. It is a mixture-of-experts model with 671 billion total parameters, 37 billion active per token, and a 128K context length.

## What Sets It Apart

DeepSeek describes a training pipeline with two reinforcement-learning stages, to find better reasoning patterns and to match human preferences, and two supervised fine-tuning stages that seed the model's reasoning and non-reasoning abilities. The card says its predecessor DeepSeek-R1-Zero was trained with reinforcement learning alone, without a supervised first step, and showed endless repetition, poor readability and language mixing, which the cold-start data in DeepSeek-R1 addresses.

## Running It

The card recommends a temperature between 0.5 and 0.7, with 0.6 suggested, to prevent endless repetition, and advises against a system prompt, with all instructions placed in the user prompt. It also suggests forcing the output to begin with the text <think> so that the model reasons before answering.
