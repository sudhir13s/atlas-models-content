---
id: model/gpt2-medium
type: model
slug: gpt2-medium
schema_version: 3.0
title: gpt2-medium
organization: organization/openai
license: license/mit
architecture_id: architecture-era/gpt
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
parameters_active_b: 0.38
total_parameters: 379988992
context_window_tokens: 1024
summary: GPT-2 Medium is the 355-million-parameter version of GPT-2, a language model created and released by OpenAI. It was pretrained on English text with a causal language modeling objective, which means it learns to predict the next word from the words before it, and it is used to generate text.
intended_use: Quoting OpenAI's GPT-2 model card, the card says the primary intended users are AI researchers and practitioners, who are expected to study the behaviors, capabilities, biases and limits of large generative language models. Secondary uses it lists are writing assistance, creative writing and entertainment such as games and chat bots. OpenAI does not support uses that require the generated text to be true.
limitations: Quoting OpenAI, the card says language models like GPT-2 do not distinguish fact from fiction and reflect the biases of the data they were trained on. OpenAI does not recommend deploying them in systems that interact with humans unless the deployers first study the biases relevant to their use.
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  vocabulary_size: 50257
  activation_function: gelu_new
  normalization: LayerNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: Learned
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 151071
  likes: 211
  last_modified: '2024-02-19'
  observed_at: '2026-11-20'
  source: https://huggingface.co/openai-community/gpt2-medium
aliases:
- openai-community/gpt2-medium
lineage:
  parents: []
  children: []
quotes:
  summary: The model is a pretrained model on English language using a causal language modeling (CLM) objective.
  intended_use: The primary intended users of these models are AI researchers and practitioners.
  limitations: Because large-scale language models like GPT-2 do not distinguish fact from fiction, we don’t support use-cases that require the generated text to be true.
tags:
- gpt2
updated: '2026-11-23'
sources:
- url: https://huggingface.co/openai-community/gpt2-medium
  title: openai-community/gpt2-medium — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai-community/gpt2-medium/blob/main/config.json
  title: openai-community/gpt2-medium — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1910.09700
  title: arXiv:1910.09700
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gpt2/modeling_gpt2.py
  title: 'transformers/models/gpt2/modeling_gpt2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


