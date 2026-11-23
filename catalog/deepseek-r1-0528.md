---
id: model/deepseek-r1-0528
type: model
slug: deepseek-r1-0528
schema_version: 3.0
title: DeepSeek-R1-0528
organization: organization/deepseek
release_date: '2025-05-28'
license: license/mit
architecture_id: architecture-era/mixture-of-experts
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
- task/reasoning-models
total_parameters: 684531386000
context_window_tokens: 163840
summary: DeepSeek-R1-0528 is a reasoning language model from DeepSeek, a minor version upgrade of its DeepSeek R1 model. A reasoning model works through a problem in a long chain of thought before giving its answer. The card says this update deepened the reasoning by using more computing during post-training and adding algorithm changes.
what_sets_it_apart: DeepSeek says the upgrade improved how deeply the model reasons, and that it also reduces the rate of hallucination (made-up answers), supports function calling better and gives a better experience for vibe coding. The card states that the model now uses more tokens when it thinks, and that DeepSeek also distilled its chain of thought into a smaller model, DeepSeek-R1-0528-Qwen3-8B.
running_it: The card says a system prompt is now supported, and the model no longer needs the text <think> added at the start of its output to make it reason. For running it locally the card points to the DeepSeek-R1 repository on GitHub.
fingerprint:
  attention_type: MLA
  num_layers: 61
  attention_heads: 128
  kv_heads: 128
  head_size: 56
  hidden_size: 7168
  intermediate_size: 18432
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
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 145131
  likes: 2496
  last_modified: '2025-05-29'
  observed_at: '2026-11-20'
  source: https://huggingface.co/deepseek-ai/DeepSeek-R1-0528
aliases:
- deepseek-ai/DeepSeek-R1-0528
lineage:
  parents: []
  children: []
quotes:
  summary: The DeepSeek R1 model has undergone a minor version upgrade, with the current version being DeepSeek-R1-0528.
  what_sets_it_apart: this version also offers a reduced hallucination rate, enhanced support for function calling, and better experience for vibe coding.
  running_it: System prompt is supported now.
tags:
- deepseek-v3
updated: '2026-11-23'
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
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/deepseek_v3/modeling_deepseek_v3.py
  title: 'transformers/models/deepseek_v3/modeling_deepseek_v3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


