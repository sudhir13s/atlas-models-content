---
id: model/qwen2-5-coder-7b-instruct
type: model
slug: qwen2-5-coder-7b-instruct
schema_version: 1.1
title: Qwen2.5-Coder-7B-Instruct
organization: organization/qwen
release_date: '2024-09-17'
release_year: 2024
release_month: 9
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
- task/code-models
parameters_active_b: 7.62
total_parameters: 7615616512
context_window_tokens: 32768
base_models:
- name: Qwen/Qwen2.5-Coder-7B
  address: https://huggingface.co/Qwen/Qwen2.5-Coder-7B
summary: Qwen2.5-Coder-7B-Instruct is the instruction-tuned 7B model of Qwen2.5-Coder, a series of code-specific language models from Qwen, formerly known as CodeQwen. It was trained further from Qwen/Qwen2.5-Coder-7B to follow instructions and chat. The card lists 7.61B parameters and a full context length of 131,072 tokens.
what_sets_it_apart: Qwen says Qwen2.5-Coder improves on CodeQwen1.5 in code generation, code reasoning and code fixing, with training data scaled up to 5.5 trillion tokens that include source code, text-code grounding and synthetic data. It also says the series keeps its strengths in mathematics and general skills and aims at real-world uses such as code agents.
running_it: The card says the config.json is set for contexts up to 32,768 tokens, and that longer inputs use YaRN, a technique for extending the length a model handles. It notes that vLLM only supports a static form of YaRN, which may affect performance on shorter texts, so the setting should be added only when long contexts are needed.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 28
  kv_heads: 4
  head_size: 128
  hidden_size: 3584
  intermediate_size: 18944
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
  downloads_30d: 1524077
  likes: 875
  last_modified: '2025-01-12'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2.5-Coder-7B-Instruct
variants:
- name: Qwen/Qwen2.5-Coder-7B-Instruct-GPTQ-Int4
  format: gptq
  address: https://huggingface.co/Qwen/Qwen2.5-Coder-7B-Instruct-GPTQ-Int4
  publisher: Qwen
aliases:
- Qwen/Qwen2.5-Coder-7B-Instruct
- qwen2.5-coder-7b-instruct
- qwen2.5-coder-7b-instruct-gptq-int4
lineage:
  parents: []
  children: []
quotes:
  summary: This repo contains the instruction-tuned 7B Qwen2.5-Coder model
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
- url: https://huggingface.co/Qwen/Qwen2.5-Coder-7B-Instruct
  title: Qwen/Qwen2.5-Coder-7B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-Coder-7B-Instruct/blob/main/config.json
  title: Qwen/Qwen2.5-Coder-7B-Instruct — config.json
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

Qwen2.5-Coder-7B-Instruct is the instruction-tuned 7B model of Qwen2.5-Coder, a series of code-specific language models from Qwen, formerly known as CodeQwen. It was trained further from Qwen/Qwen2.5-Coder-7B to follow instructions and chat. The card lists 7.61B parameters and a full context length of 131,072 tokens.

## What Sets It Apart

Qwen says Qwen2.5-Coder improves on CodeQwen1.5 in code generation, code reasoning and code fixing, with training data scaled up to 5.5 trillion tokens that include source code, text-code grounding and synthetic data. It also says the series keeps its strengths in mathematics and general skills and aims at real-world uses such as code agents.

## Running It

The card says the config.json is set for contexts up to 32,768 tokens, and that longer inputs use YaRN, a technique for extending the length a model handles. It notes that vLLM only supports a static form of YaRN, which may affect performance on shorter texts, so the setting should be added only when long contexts are needed.
