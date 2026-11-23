---
id: model/qwen2-5-coder-32b-instruct
type: model
slug: qwen2-5-coder-32b-instruct
schema_version: 1.1
title: Qwen2.5-Coder-32B-Instruct
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
parameters_active_b: 32.76
total_parameters: 32763876352
context_window_tokens: 32768
base_models:
- name: Qwen/Qwen2.5-Coder-32B
  address: https://huggingface.co/Qwen/Qwen2.5-Coder-32B
summary: 'Qwen2.5-Coder-32B-Instruct is a language model from Qwen that is specialised for code: it reads a request in text and writes code or an explanation in reply. It is the instruction-tuned 32B member of the Qwen2.5-Coder series, which the card says comes in six sizes from 0.5 to 32 billion parameters and was formerly known as CodeQwen. The card gives it 32.5 billion parameters and a context length of 131,072 tokens.'
what_sets_it_apart: Qwen states that, compared with CodeQwen1.5, Qwen2.5-Coder improves code generation, code reasoning and code fixing, built on Qwen2.5 with training scaled up to 5.5 trillion tokens of source code, text-code grounding and synthetic data. It also says the series aims to serve real-world uses such as code agents while keeping its strengths in mathematics and general skills.
running_it: The card says the published config.json is set for a context of up to 32,768 tokens, and that longer inputs rely on YaRN, a technique for extending the length a model handles. It says vLLM supports only static YaRN, which may affect performance on shorter texts, so the setting should be added only when long contexts are needed.
fingerprint:
  attention_type: GQA
  num_layers: 64
  attention_heads: 40
  kv_heads: 8
  head_size: 128
  hidden_size: 5120
  intermediate_size: 27648
  context_window_tokens: 32768
  vocabulary_size: 152064
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: large
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 513256
  likes: 2216
  last_modified: '2025-01-12'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2.5-Coder-32B-Instruct
aliases:
- Qwen/Qwen2.5-Coder-32B-Instruct
- qwen2.5-coder-32b-instruct
lineage:
  parents: []
  children: []
quotes:
  summary: This repo contains the instruction-tuned 32B Qwen2.5-Coder model
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
- url: https://huggingface.co/Qwen/Qwen2.5-Coder-32B-Instruct
  title: Qwen/Qwen2.5-Coder-32B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-Coder-32B-Instruct/blob/main/config.json
  title: Qwen/Qwen2.5-Coder-32B-Instruct — config.json
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

Qwen2.5-Coder-32B-Instruct is a language model from Qwen that is specialised for code: it reads a request in text and writes code or an explanation in reply. It is the instruction-tuned 32B member of the Qwen2.5-Coder series, which the card says comes in six sizes from 0.5 to 32 billion parameters and was formerly known as CodeQwen. The card gives it 32.5 billion parameters and a context length of 131,072 tokens.

## What Sets It Apart

Qwen states that, compared with CodeQwen1.5, Qwen2.5-Coder improves code generation, code reasoning and code fixing, built on Qwen2.5 with training scaled up to 5.5 trillion tokens of source code, text-code grounding and synthetic data. It also says the series aims to serve real-world uses such as code agents while keeping its strengths in mathematics and general skills.

## Running It

The card says the published config.json is set for a context of up to 32,768 tokens, and that longer inputs rely on YaRN, a technique for extending the length a model handles. It says vLLM supports only static YaRN, which may affect performance on shorter texts, so the setting should be added only when long contexts are needed.
