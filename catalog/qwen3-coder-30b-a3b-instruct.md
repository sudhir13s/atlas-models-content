---
id: model/qwen3-coder-30b-a3b-instruct
type: model
slug: qwen3-coder-30b-a3b-instruct
schema_version: 3.0
title: Qwen3-Coder-30B-A3B-Instruct
organization: organization/qwen
release_date: '2025-07-31'
license: license/apache-2-0
architecture_id: architecture-era/mixture-of-experts
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
- task/code-models
- task/tool-calling-models
total_parameters: 30532122624
context_window_tokens: 262144
summary: 'Qwen3-Coder-30B-A3B-Instruct is a code-focused language model from Qwen. It is a mixture-of-experts model: the card gives 30.5 billion parameters in total with 3.3 billion activated per token, using 8 of 128 experts. The card says it supports only non-thinking mode, so it does not write reasoning blocks before its answer.'
intended_use: The card presents the model for agentic coding, where the model works through programming tasks using tools, and says it works with platforms such as Qwen Code and CLINE. It describes the long context as optimized for repository-scale understanding.
what_sets_it_apart: Qwen states a native context of 262,144 tokens, extendable to 1 million tokens with YaRN, and a specially designed function call format for agentic coding.
running_it: The card suggests temperature=0.7, top_p=0.8, top_k=20 and repetition_penalty=1.05, and an output length of 65,536 tokens for most queries. It says Transformers 4.51.0 or later is needed.
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 32
  kv_heads: 4
  head_size: 128
  hidden_size: 2048
  intermediate_size: 6144
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 10000000.0
moe_config:
  expert_count: 128
  active_experts: 8
filters:
  size_category: large
  is_quantized: false
  is_moe: true
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 1956579
  likes: 1347
  last_modified: '2025-12-03'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-Coder-30B-A3B-Instruct
aliases:
- Qwen/Qwen3-Coder-30B-A3B-Instruct
lineage:
  parents: []
  children: []
quotes:
  summary: This model supports only non-thinking mode
  intended_use: optimized for repository-scale understanding.
  what_sets_it_apart: featuring a specially designed function call format.
  running_it: We suggest using `temperature=0.7`, `top_p=0.8`, `top_k=20`, `repetition_penalty=1.05`.
tags:
- qwen3-moe
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-Coder-30B-A3B-Instruct
  title: Qwen/Qwen3-Coder-30B-A3B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-Coder-30B-A3B-Instruct/blob/main/config.json
  title: Qwen/Qwen3-Coder-30B-A3B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3_moe/modeling_qwen3_moe.py
  title: 'transformers/models/qwen3_moe/modeling_qwen3_moe.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


