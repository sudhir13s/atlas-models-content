---
id: model/qwen2-5-coder-14b-instruct
type: model
slug: qwen2-5-coder-14b-instruct
schema_version: 1.1
title: Qwen2.5-Coder-14B-Instruct
organization: organization/qwen
release_date: '2024-11-06'
release_year: 2024
release_month: 11
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
- task/code-models
parameters_active_b: 14.77
total_parameters: 14770033664
context_window_tokens: 32768
base_models:
- name: Qwen/Qwen2.5-Coder-14B
  address: https://huggingface.co/Qwen/Qwen2.5-Coder-14B
summary: Qwen2.5 Coder 14B Instruct is a language model from Qwen that reads text and writes text in reply, with a focus on code. It is the instruction-tuned member of the Qwen2.5-Coder series at the 14B size, which means the base model was trained further to follow instructions. The card says the series, formerly known as CodeQwen, covers six sizes from 0.5 to 32 billion parameters, and lists 14.7B parameters and a full context of 131,072 tokens for this model.
what_sets_it_apart: Qwen states that, compared with CodeQwen1.5, Qwen2.5-Coder improves code generation, code reasoning and code fixing, and that training tokens were scaled up to 5.5 trillion, including source code, text-code grounding and synthetic data. The card also says it aims to be a base for real-world applications such as code agents while keeping its strengths in mathematics and general competencies.
running_it: The card says the published config.json is set for a context of up to 32,768 tokens, and that longer inputs rely on YaRN, a technique for extending the length a model handles. It notes that vLLM supports only static YaRN, which may affect performance on shorter texts.
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 40
  kv_heads: 8
  head_size: 128
  hidden_size: 5120
  intermediate_size: 13824
  context_window_tokens: 32768
  vocabulary_size: 152064
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 1579537
  likes: 220
  last_modified: '2025-01-12'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2.5-Coder-14B-Instruct
aliases:
- Qwen/Qwen2.5-Coder-14B-Instruct
- qwen2.5-coder-14b-instruct
lineage:
  parents: []
  children: []
quotes:
  summary: This repo contains the instruction-tuned 14B Qwen2.5-Coder model
  what_sets_it_apart: we scale up the training tokens into 5.5 trillion including source code, text-code grounding, Synthetic data, etc.
  running_it: The current `config.json` is set for context length up to 32,768 tokens.
tags:
- llm
- text-generation
- qwen2
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-Coder-14B-Instruct
  title: Qwen/Qwen2.5-Coder-14B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-Coder-14B-Instruct/blob/main/config.json
  title: Qwen/Qwen2.5-Coder-14B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2409.12186
  title: arXiv:2409.12186
  trust: 0.95
- url: https://arxiv.org/abs/2309.00071
  title: arXiv:2309.00071
  trust: 0.95
- url: https://arxiv.org/abs/2407.10671
  title: arXiv:2407.10671
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen2/modeling_qwen2.py
  title: 'transformers/models/qwen2/modeling_qwen2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Qwen2.5 Coder 14B Instruct is a language model from Qwen that reads text and writes text in reply, with a focus on code. It is the instruction-tuned member of the Qwen2.5-Coder series at the 14B size, which means the base model was trained further to follow instructions. The card says the series, formerly known as CodeQwen, covers six sizes from 0.5 to 32 billion parameters, and lists 14.7B parameters and a full context of 131,072 tokens for this model.

## What Sets It Apart

Qwen states that, compared with CodeQwen1.5, Qwen2.5-Coder improves code generation, code reasoning and code fixing, and that training tokens were scaled up to 5.5 trillion, including source code, text-code grounding and synthetic data. The card also says it aims to be a base for real-world applications such as code agents while keeping its strengths in mathematics and general competencies.

## Running It

The card says the published config.json is set for a context of up to 32,768 tokens, and that longer inputs rely on YaRN, a technique for extending the length a model handles. It notes that vLLM supports only static YaRN, which may affect performance on shorter texts.
