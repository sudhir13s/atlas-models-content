---
id: model/gpt-oss-120b
type: model
slug: gpt-oss-120b
schema_version: 3.0
title: gpt-oss-120b
organization: organization/openai
release_date: '2025-08-04'
license: license/apache-2-0
architecture_id: architecture-era/mixture-of-experts
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
- task/reasoning-models
- task/tool-calling-models
total_parameters: 116829156672
context_window_tokens: 131072
summary: gpt-oss-120b is an open-weight language model from OpenAI, meaning its trained weights are published for anyone to download and run. The card lists 117B parameters with 5.1B active, so it is a mixture-of-experts model in which only part of the network is used for each token, and says it fits on a single 80GB GPU. The card says it is built for reasoning, agentic tasks and developer use cases.
intended_use: The card describes gpt-oss-120b as the larger of two models, meant for production, general-purpose and high-reasoning use that fits on a single 80GB GPU. It lists agentic uses through native function calling, web browsing, Python code execution and structured outputs, and says the model can be fine-tuned. It states that the full chain-of-thought is not intended to be shown to end users.
what_sets_it_apart: The card highlights a configurable reasoning effort (low, medium or high) and full access to the model's chain-of-thought, its written-out reasoning, for debugging. It also says the model was post-trained with MXFP4 quantization of the mixture-of-experts weights, which the card says lets it run on one 80GB GPU, and that all of its evaluations used the same quantization. The license is Apache 2.0.
running_it: The card says both gpt-oss models were trained on OpenAI's harmony response format and will not work correctly without it. The Transformers chat template applies the format automatically, while direct calls to model.generate need the format applied by hand or through the openai-harmony package.
fingerprint:
  attention_type: GQA
  num_layers: 36
  attention_heads: 64
  kv_heads: 8
  head_size: 64
  hidden_size: 2880
  intermediate_size: 2880
  vocabulary_size: 201088
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 150000.0
moe_config:
  expert_count: 128
  active_experts: 4
filters:
  size_category: xlarge
  is_quantized: true
  quant_format: mxfp4
  is_moe: true
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 2395705
  likes: 5546
  last_modified: '2025-08-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/openai/gpt-oss-120b
aliases:
- openai/gpt-oss-120b
lineage:
  parents: []
  children: []
quotes:
  summary: (117B parameters with 5.1B active parameters)
  intended_use: for production, general purpose, high reasoning use cases that fit into a single 80GB GPU
  what_sets_it_apart: Easily adjust the reasoning effort (low, medium, high) based on your specific use case and latency needs.
  running_it: should only be used with the harmony format as it will not work correctly otherwise.
tags:
- gpt-oss
updated: '2026-11-23'
sources:
- url: https://huggingface.co/openai/gpt-oss-120b
  title: openai/gpt-oss-120b — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai/gpt-oss-120b/blob/main/config.json
  title: openai/gpt-oss-120b — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2508.10925
  title: arXiv:2508.10925
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gpt_oss/modeling_gpt_oss.py
  title: 'transformers/models/gpt_oss/modeling_gpt_oss.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


