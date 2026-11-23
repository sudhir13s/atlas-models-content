---
id: model/deepseek-v3-2
type: model
slug: deepseek-v3-2
schema_version: 3.0
title: DeepSeek-V3.2
organization: organization/deepseek
release_date: '2025-12-01'
license: license/mit
architecture_id: architecture-era/mixture-of-experts
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
total_parameters: 685355329792
context_window_tokens: 163840
base_models:
- name: deepseek-ai/DeepSeek-V3.2-Exp-Base
  address: https://huggingface.co/deepseek-ai/DeepSeek-V3.2-Exp-Base
summary: 'DeepSeek-V3.2 is a large language model from DeepSeek, aimed at reasoning and at agent work, where a model calls tools to finish a task. The model card lists DeepSeek-V3.2-Exp-Base as its base model, so it is a further-trained version of that model. The card names three main ideas behind it: a more efficient attention mechanism, a scaled-up reinforcement learning stage, and a pipeline that generates training data for tool use.'
what_sets_it_apart: DeepSeek says it introduced DeepSeek Sparse Attention (DSA), an attention method meant to cut the computing cost of long inputs while keeping model quality. It also says it built a large-scale pipeline that synthesizes agent tasks as training data, to bring reasoning into tool-use settings.
running_it: The card says the chat template changed significantly from earlier versions, with a new format for tool calls and a "thinking with tools" capability. It supplies an encoding folder with Python scripts and tests showing how to turn OpenAI-style messages into model input and parse the output.
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
  downloads_30d: 10436104
  likes: 1608
  last_modified: '2025-12-01'
  observed_at: '2026-11-20'
  source: https://huggingface.co/deepseek-ai/DeepSeek-V3.2
aliases:
- deepseek-ai/DeepSeek-V3.2
- deepseek-v3.2
lineage:
  parents: []
  children: []
quotes:
  summary: We introduce **DeepSeek-V3.2**, a model that harmonizes high computational efficiency with superior reasoning and agent performance.
  what_sets_it_apart: We introduce DSA, an efficient attention mechanism that substantially reduces computational complexity while preserving model performance, specifically optimized for long-context scenarios.
  running_it: DeepSeek-V3.2 introduces significant updates to its chat template compared to prior versions.
tags:
- deepseek-v32
updated: '2026-11-23'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-V3.2
  title: deepseek-ai/DeepSeek-V3.2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-V3.2/blob/main/config.json
  title: deepseek-ai/DeepSeek-V3.2 — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/deepseek_v32/modeling_deepseek_v32.py
  title: 'transformers/models/deepseek_v32/modeling_deepseek_v32.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


