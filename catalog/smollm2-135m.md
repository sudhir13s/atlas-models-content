---
id: model/smollm2-135m
type: model
slug: smollm2-135m
schema_version: 1.1
title: SmolLM2-135M
organization: organization/hugging-face
release_date: '2024-10-31'
release_year: 2024
release_month: 10
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 0.13
total_parameters: 134515008
context_window_tokens: 8192
summary: SmolLM2-135M is the smallest model of the SmolLM2 family of compact English language models from the Hugging Face team. The card says the family comes in three sizes, 135M, 360M and 1.7B parameters, and that the models are lightweight enough to run on-device.
intended_use: The card says the SmolLM2 models can solve a wide range of tasks while being lightweight enough to run on-device. It adds that the instruct versions also support text rewriting, summarization and function calling for the 1.7B size.
what_sets_it_apart: The card says the 135M model was trained on 2 trillion tokens from a mix of FineWeb-Edu, DCLM, The Stack and newly filtered datasets, and describes a transformer decoder trained in bfloat16 precision. It separately describes an instruct version, built by supervised fine-tuning on public and curated data followed by Direct Preference Optimization.
limitations: The card says the models mainly understand and generate English, and that their output may not be factually accurate, logically consistent or free from bias, so they should be used as assistive tools rather than as sources of truth.
fingerprint:
  attention_type: GQA
  num_layers: 30
  attention_heads: 9
  kv_heads: 3
  head_size: 64
  hidden_size: 576
  intermediate_size: 1536
  context_window_tokens: 8192
  vocabulary_size: 49152
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 100000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 2091998
  likes: 273
  last_modified: '2025-02-06'
  observed_at: '2026-11-20'
  source: https://huggingface.co/HuggingFaceTB/SmolLM2-135M
aliases:
- HuggingFaceTB/SmolLM2-135M
lineage:
  parents: []
  children: []
quotes:
  summary: 'SmolLM2 is a family of compact language models available in three size: 135M, 360M, and 1.7B parameters.'
  intended_use: lightweight enough to run on-device
  what_sets_it_apart: 'The 135M model was trained on 2 trillion tokens using a diverse dataset combination: FineWeb-Edu, DCLM, The Stack'
  limitations: These models should be used as assistive tools rather than definitive sources of information.
tags:
- llm
- text-generation
- llama
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/HuggingFaceTB/SmolLM2-135M
  title: HuggingFaceTB/SmolLM2-135M — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/HuggingFaceTB/SmolLM2-135M/blob/main/config.json
  title: HuggingFaceTB/SmolLM2-135M — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2502.02737
  title: arXiv:2502.02737
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/modeling_llama.py
  title: 'transformers/models/llama/modeling_llama.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

SmolLM2-135M is the smallest model of the SmolLM2 family of compact English language models from the Hugging Face team. The card says the family comes in three sizes, 135M, 360M and 1.7B parameters, and that the models are lightweight enough to run on-device.

## Intended Use

The card says the SmolLM2 models can solve a wide range of tasks while being lightweight enough to run on-device. It adds that the instruct versions also support text rewriting, summarization and function calling for the 1.7B size.

## What Sets It Apart

The card says the 135M model was trained on 2 trillion tokens from a mix of FineWeb-Edu, DCLM, The Stack and newly filtered datasets, and describes a transformer decoder trained in bfloat16 precision. It separately describes an instruct version, built by supervised fine-tuning on public and curated data followed by Direct Preference Optimization.

## Limitations

The card says the models mainly understand and generate English, and that their output may not be factually accurate, logically consistent or free from bias, so they should be used as assistive tools rather than as sources of truth.
