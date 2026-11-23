---
id: model/phi-4-mini-instruct
type: model
slug: phi-4-mini-instruct
schema_version: 3.0
title: Phi-4-mini-instruct
organization: organization/microsoft
release_date: '2025-02-19'
license: license/mit
architecture_id: architecture-era/llama-recipe
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
- task/tool-calling-models
parameters_active_b: 3.84
total_parameters: 3836021760
context_window_tokens: 131072
summary: Phi-4-mini-instruct is a small open language model from Microsoft, part of the Phi-4 family. The card says it was built on synthetic data and filtered public websites, with a focus on reasoning-dense data, and that it supports a 128K token context. It was then post-trained with supervised fine-tuning and direct preference optimization, two training steps for following instructions and for safety. The card gives its size as 3.8B parameters.
intended_use: The card says it is intended for broad multilingual commercial and research use, in general-purpose AI systems that need to run in memory or compute constrained environments, in latency-bound scenarios, or that need strong reasoning, especially math and logic. It also says the model is meant to speed up research on language and multimodal models and to serve as a building block for generative AI features. The card warns that the model is not designed or evaluated for every downstream purpose, so developers should evaluate accuracy, safety and fairness first, particularly in high-risk scenarios.
what_sets_it_apart: The card's release notes say the Phi-4-mini model uses a new architecture for efficiency, a larger vocabulary for multilingual support, and better post-training techniques for instruction following and function calling, compared with the Phi-3 series that came before. The training data is described as synthetic data plus filtered public websites, chosen for reasoning-dense content.
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 24
  kv_heads: 8
  head_size: 128
  hidden_size: 3072
  intermediate_size: 8192
  vocabulary_size: 200064
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 304616
  likes: 892
  last_modified: '2025-12-10'
  observed_at: '2026-11-20'
  source: https://huggingface.co/microsoft/Phi-4-mini-instruct
aliases:
- microsoft/Phi-4-mini-instruct
lineage:
  parents: []
  children: []
quotes:
  summary: Phi-4-mini-instruct is a lightweight open model built upon synthetic data and filtered publicly available websites
  intended_use: The model is intended for broad multilingual commercial and research use.
  what_sets_it_apart: The Phi-4-mini model employed new architecture for efficiency, larger vocabulary for multilingual support
tags:
- phi3
updated: '2026-11-23'
sources:
- url: https://huggingface.co/microsoft/Phi-4-mini-instruct
  title: microsoft/Phi-4-mini-instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/Phi-4-mini-instruct/blob/main/config.json
  title: microsoft/Phi-4-mini-instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2503.01743
  title: arXiv:2503.01743
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/phi3/modeling_phi3.py
  title: 'transformers/models/phi3/modeling_phi3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


