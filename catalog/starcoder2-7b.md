---
id: model/starcoder2-7b
type: model
slug: starcoder2-7b
schema_version: 1.1
title: starcoder2-7b
organization: organization/bigcode
release_date: '2024-02-20'
release_year: 2024
release_month: 2
license: license/bigcode-openrail-m
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
- task/code-models
parameters_active_b: 7.17
total_parameters: 7173923840
context_window_tokens: 16384
summary: StarCoder2-7B is a code generation model from the BigCode project with 7 billion parameters. The card says it was trained on 17 programming languages from The Stack v2, a code dataset, with opt-out requests excluded, plus selected sources such as Arxiv and Wikipedia. It continues code that it is given, and the card says it is not an instruction model.
intended_use: The card says the model is capable of generating code snippets from the context it is given. It states that, because it is not an instruction model, commands such as asking it to write a function that computes the square root do not work well.
what_sets_it_apart: The card lists Grouped Query Attention, in which several query heads share key and value heads, a context window of 16,384 tokens with a sliding window attention of 4,096 tokens, and training with the Fill-in-the-Middle objective, which teaches the model to write code that fits between a given beginning and end. It states training on more than 3.5 trillion tokens.
limitations: The card says the generated code is not guaranteed to work as intended and can be inefficient or contain bugs or exploits. It notes that the predominant language in the source code is English, with other languages also present.
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 36
  kv_heads: 4
  head_size: 128
  hidden_size: 4608
  intermediate_size: 18432
  context_window_tokens: 16384
  vocabulary_size: 49152
  activation_function: gelu_pytorch_tanh
  normalization: LayerNorm
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
  downloads_30d: 17279
  likes: 227
  last_modified: '2024-06-11'
  observed_at: '2026-11-20'
  source: https://huggingface.co/bigcode/starcoder2-7b
aliases:
- bigcode/starcoder2-7b
lineage:
  parents: []
  children: []
quotes:
  summary: StarCoder2-7B model is a 7B parameter model trained on 17 programming languages
  intended_use: As such it is _not_ an instruction model
  what_sets_it_apart: on 3.5+ trillion tokens.
  limitations: the generated code is not guaranteed to work as intended
tags:
- llm
- text-generation
- starcoder2
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/bigcode/starcoder2-7b
  title: bigcode/starcoder2-7b — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/bigcode/starcoder2-7b/blob/main/config.json
  title: bigcode/starcoder2-7b — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2305.13245
  title: arXiv:2305.13245
  trust: 0.95
- url: https://arxiv.org/abs/2205.14135
  title: arXiv:2205.14135
  trust: 0.95
- url: https://arxiv.org/abs/2004.05150
  title: arXiv:2004.05150
  trust: 0.95
- url: https://arxiv.org/abs/2207.14255
  title: arXiv:2207.14255
  trust: 0.95
- url: https://arxiv.org/abs/2402.19173
  title: arXiv:2402.19173
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/starcoder2/modeling_starcoder2.py
  title: 'transformers/models/starcoder2/modeling_starcoder2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

StarCoder2-7B is a code generation model from the BigCode project with 7 billion parameters. The card says it was trained on 17 programming languages from The Stack v2, a code dataset, with opt-out requests excluded, plus selected sources such as Arxiv and Wikipedia. It continues code that it is given, and the card says it is not an instruction model.

## Intended Use

The card says the model is capable of generating code snippets from the context it is given. It states that, because it is not an instruction model, commands such as asking it to write a function that computes the square root do not work well.

## What Sets It Apart

The card lists Grouped Query Attention, in which several query heads share key and value heads, a context window of 16,384 tokens with a sliding window attention of 4,096 tokens, and training with the Fill-in-the-Middle objective, which teaches the model to write code that fits between a given beginning and end. It states training on more than 3.5 trillion tokens.

## Limitations

The card says the generated code is not guaranteed to work as intended and can be inefficient or contain bugs or exploits. It notes that the predominant language in the source code is English, with other languages also present.
