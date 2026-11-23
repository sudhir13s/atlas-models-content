---
id: model/openelm-1-1b-instruct
type: model
slug: openelm-1-1b-instruct
schema_version: 3.0
title: OpenELM-1_1B-Instruct
organization: organization/apple
release_date: '2024-04-12'
license: license/apple-amlr
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
parameters_active_b: 1.08
total_parameters: 1079891456
context_window_tokens: 2048
summary: OpenELM-1_1B-Instruct is a small language model from Apple, with 1.1 billion parameters, that was instruction-tuned so it can follow prompts. It belongs to OpenELM, which the card calls a family of Open Efficient Language Models released in pretrained and instruction-tuned versions at 270M, 450M, 1.1B and 3B parameters. The card says the pre-training data totals approximately 1.8 trillion tokens.
what_sets_it_apart: The card says OpenELM uses a layer-wise scaling strategy to allocate parameters within each layer of the transformer, and credits this for better accuracy. Apple also states that it released the complete framework for data preparation, training, fine-tuning and evaluation, together with checkpoints and training logs, to support open research.
running_it: 'The card provides a generate_openelm.py script for models loaded from the Hugging Face Hub, run with repetition_penalty=1.2 in its example. It also describes two ways to speed up generation: prompt lookup speculative generation, and an assistant model, a smaller model that drafts tokens for the larger one.'
fingerprint:
  num_layers: 28
  head_size: 64
  hidden_size: 2048
  vocabulary_size: 32000
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 866
  likes: 78
  last_modified: '2025-02-28'
  observed_at: '2026-11-20'
  source: https://huggingface.co/apple/OpenELM-1_1B-Instruct
aliases:
- apple/OpenELM-1_1B-Instruct
lineage:
  parents: []
  children: []
quotes:
  summary: We release both pretrained and instruction tuned models with 270M, 450M, 1.1B and 3B parameters.
  what_sets_it_apart: OpenELM uses a layer-wise scaling strategy to efficiently allocate parameters within each layer of the transformer model, leading to enhanced accuracy.
  running_it: Additional arguments to the hugging face generate function can be passed via `generate_kwargs`.
tags:
- openelm
updated: '2026-11-23'
sources:
- url: https://huggingface.co/apple/OpenELM-1_1B-Instruct
  title: apple/OpenELM-1_1B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/apple/OpenELM-1_1B-Instruct/blob/main/config.json
  title: apple/OpenELM-1_1B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2404.14619
  title: arXiv:2404.14619
  trust: 0.95
- url: https://huggingface.co/apple/OpenELM-1_1B-Instruct/blob/main/modeling_openelm.py
  title: 'apple/OpenELM-1_1B-Instruct: modeling_openelm.py (official implementation)'
  trust: 0.9
---


