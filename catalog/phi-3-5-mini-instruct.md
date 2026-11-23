---
id: model/phi-3-5-mini-instruct
type: model
slug: phi-3-5-mini-instruct
schema_version: 1.1
title: Phi-3.5-mini-instruct
organization: organization/microsoft
release_date: '2024-08-16'
release_year: 2024
release_month: 8
license: license/mit
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 3.82
total_parameters: 3821079552
context_window_tokens: 131072
summary: Phi-3.5-mini-instruct is a small open language model from Microsoft with 3.8B parameters, a dense decoder-only transformer in the Phi-3 model family. The card lists a 128K-token context length and 23 supported languages. It describes the model as an update over the June 2024 Phi-3 Mini instruct release, trained with additional post-training data.
intended_use: The card says it is intended for commercial and research use in multiple languages, in general purpose AI systems that run in memory or compute constrained environments, need low latency, or need strong reasoning, especially in code, math and logic. It says the model is not specifically designed or evaluated for all downstream uses, and developers should evaluate accuracy, safety and fairness before using it, particularly in high-risk scenarios.
what_sets_it_apart: 'Microsoft says it builds on the datasets used for Phi-3: synthetic data and filtered public websites, with a focus on high-quality, reasoning-dense data. Training used 3.4 trillion tokens. Instruction following and safety came from supervised fine-tuning, proximal policy optimization and direct preference optimization.'
running_it: The card says the model is best suited to prompts in a chat format that marks the system, user and assistant turns with tags such as <|system|>, <|user|> and <|assistant|>, each turn closed by <|end|>. It notes that the Phi-3 family is integrated in Transformers version 4.43.0 and that the vocabulary holds up to 32064 tokens.
limitations: The card says the Phi models are trained mainly on English, so other languages perform worse, with gaps between non-English languages. Most of the code training data is Python with common packages, so scripts using other packages or languages should be checked by hand. It adds that long chat sessions can become repetitive or inconsistent, and that outputs can be inaccurate, biased or offensive.
fingerprint:
  attention_type: MHA
  num_layers: 32
  attention_heads: 32
  kv_heads: 32
  head_size: 96
  hidden_size: 3072
  intermediate_size: 8192
  context_window_tokens: 131072
  vocabulary_size: 32064
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
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 297535
  likes: 1297
  last_modified: '2025-12-10'
  observed_at: '2026-11-20'
  source: https://huggingface.co/microsoft/Phi-3.5-mini-instruct
gguf_repositories:
  repositories:
  - publisher: bartowski
    repo_id: bartowski/Phi-3.5-mini-instruct-GGUF
    address: https://huggingface.co/bartowski/Phi-3.5-mini-instruct-GGUF
  observed_at: '2026-11-23'
  source: https://huggingface.co/models?library=gguf&other=base_model:quantized:microsoft/Phi-3.5-mini-instruct
aliases:
- microsoft/Phi-3.5-mini-instruct
- phi-3.5-mini-instruct
lineage:
  parents: []
  children: []
quotes:
  summary: The model belongs to the Phi-3 model family and supports 128K token context length.
  intended_use: The model is intended for commercial and research use in multiple languages.
  what_sets_it_apart: The model underwent a rigorous enhancement process, incorporating both supervised fine-tuning, proximal policy optimization, and direct preference optimization
  running_it: 'the Phi-3.5-mini-instruct model is best suited for prompts using the chat format as follows:'
  limitations: Languages other than English will experience worse performance as well as performance disparities across non-English.
tags:
- llm
- text-generation
- phi3
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/microsoft/Phi-3.5-mini-instruct
  title: microsoft/Phi-3.5-mini-instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/Phi-3.5-mini-instruct/blob/main/config.json
  title: microsoft/Phi-3.5-mini-instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2404.14219
  title: arXiv:2404.14219
  trust: 0.95
- url: https://arxiv.org/abs/2407.13833
  title: arXiv:2407.13833
  trust: 0.95
- url: https://arxiv.org/abs/2403.06412
  title: arXiv:2403.06412
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/phi3/modeling_phi3.py
  title: 'transformers/models/phi3/modeling_phi3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Phi-3.5-mini-instruct is a small open language model from Microsoft with 3.8B parameters, a dense decoder-only transformer in the Phi-3 model family. The card lists a 128K-token context length and 23 supported languages. It describes the model as an update over the June 2024 Phi-3 Mini instruct release, trained with additional post-training data.

## Intended Use

The card says it is intended for commercial and research use in multiple languages, in general purpose AI systems that run in memory or compute constrained environments, need low latency, or need strong reasoning, especially in code, math and logic. It says the model is not specifically designed or evaluated for all downstream uses, and developers should evaluate accuracy, safety and fairness before using it, particularly in high-risk scenarios.

## What Sets It Apart

Microsoft says it builds on the datasets used for Phi-3: synthetic data and filtered public websites, with a focus on high-quality, reasoning-dense data. Training used 3.4 trillion tokens. Instruction following and safety came from supervised fine-tuning, proximal policy optimization and direct preference optimization.

## Running It

The card says the model is best suited to prompts in a chat format that marks the system, user and assistant turns with tags such as <|system|>, <|user|> and <|assistant|>, each turn closed by <|end|>. It notes that the Phi-3 family is integrated in Transformers version 4.43.0 and that the vocabulary holds up to 32064 tokens.

## Limitations

The card says the Phi models are trained mainly on English, so other languages perform worse, with gaps between non-English languages. Most of the code training data is Python with common packages, so scripts using other packages or languages should be checked by hand. It adds that long chat sessions can become repetitive or inconsistent, and that outputs can be inaccurate, biased or offensive.
