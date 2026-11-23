---
id: model/smollm2-1-7b-instruct
type: model
slug: smollm2-1-7b-instruct
schema_version: 1.1
title: SmolLM2-1.7B-Instruct
organization: organization/hugging-face
release_date: '2024-10-31'
release_year: 2024
release_month: 10
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 1.71
total_parameters: 1711376384
context_window_tokens: 8192
base_models:
- name: HuggingFaceTB/SmolLM2-1.7B
  address: https://huggingface.co/HuggingFaceTB/SmolLM2-1.7B
summary: SmolLM2-1.7B-Instruct is the instruction-tuned 1.7-billion-parameter model of SmolLM2, a family of compact language models from Hugging Face that the card says are light enough to run on-device. The card says the base model was trained on 11 trillion tokens, and the instruct version was then trained further to follow instructions.
intended_use: The card says the instruct model supports tasks such as text rewriting, summarization and function calling, and that the models in the family can solve a wide range of tasks while being light enough to run on a device.
what_sets_it_apart: The card says the 1.7B model improves on its predecessor SmolLM1-1.7B in instruction following, knowledge, reasoning and mathematics. The instruct version was built with supervised fine-tuning on public and self-curated datasets, followed by Direct Preference Optimization, a method that teaches the model from ranked answer pairs, using UltraFeedback.
limitations: The card says SmolLM2 models mainly understand and produce English, and their output may not always be factually accurate, logically consistent or free of bias from the training data. They should be treated as assistive tools rather than definitive sources, and important information should be verified.
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 32
  kv_heads: 32
  head_size: 64
  hidden_size: 2048
  intermediate_size: 8192
  context_window_tokens: 8192
  vocabulary_size: 49152
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 130000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 245570
  likes: 772
  last_modified: '2025-04-21'
  observed_at: '2026-11-20'
  source: https://huggingface.co/HuggingFaceTB/SmolLM2-1.7B-Instruct
aliases:
- HuggingFaceTB/SmolLM2-1.7B-Instruct
- smollm2-1.7b-instruct
lineage:
  parents: []
  children: []
quotes:
  summary: 'SmolLM2 is a family of compact language models available in three size: 135M, 360M, and 1.7B parameters.'
  intended_use: The instruct model additionally supports tasks such as text rewriting, summarization and function calling
  what_sets_it_apart: We developed the instruct version through supervised fine-tuning (SFT) using a combination of public datasets and our own curated datasets.
  limitations: SmolLM2 models primarily understand and generate content in English.
tags:
- llm
- text-generation
- llama
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/HuggingFaceTB/SmolLM2-1.7B-Instruct
  title: HuggingFaceTB/SmolLM2-1.7B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/HuggingFaceTB/SmolLM2-1.7B-Instruct/blob/main/config.json
  title: HuggingFaceTB/SmolLM2-1.7B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2502.02737
  title: arXiv:2502.02737
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/modeling_llama.py
  title: 'transformers/models/llama/modeling_llama.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

SmolLM2-1.7B-Instruct is the instruction-tuned 1.7-billion-parameter model of SmolLM2, a family of compact language models from Hugging Face that the card says are light enough to run on-device. The card says the base model was trained on 11 trillion tokens, and the instruct version was then trained further to follow instructions.

## Intended Use

The card says the instruct model supports tasks such as text rewriting, summarization and function calling, and that the models in the family can solve a wide range of tasks while being light enough to run on a device.

## What Sets It Apart

The card says the 1.7B model improves on its predecessor SmolLM1-1.7B in instruction following, knowledge, reasoning and mathematics. The instruct version was built with supervised fine-tuning on public and self-curated datasets, followed by Direct Preference Optimization, a method that teaches the model from ranked answer pairs, using UltraFeedback.

## Limitations

The card says SmolLM2 models mainly understand and produce English, and their output may not always be factually accurate, logically consistent or free of bias from the training data. They should be treated as assistive tools rather than definitive sources, and important information should be verified.
