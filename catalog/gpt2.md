---
id: model/gpt2
type: model
slug: gpt2
schema_version: 3.0
title: gpt2
organization: organization/openai
license: license/mit
architecture_id: architecture-era/gpt
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
parameters_active_b: 0.14
total_parameters: 137022720
context_window_tokens: 1024
summary: GPT-2 is an English language model from OpenAI that writes text by repeatedly predicting the next word. This is the smallest version of GPT-2, with 124M parameters. The card says it was pretrained on a very large corpus of English text with a causal language modeling objective, and that the Hugging Face team extended the model card written by the release team.
intended_use: 'The card says the raw model can be used for text generation or fine-tuned for a downstream task, and that it is best at what it was pretrained for: generating text from a prompt.'
what_sets_it_apart: The card describes self-supervised pretraining on raw text with no human labels, where the target is the same text shifted one token to the right. A mask inside the model makes the prediction for each token use only the tokens before it, never the ones after. The card says this lets the model learn an internal representation of English that can also supply features for other tasks.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
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
  downloads_30d: 34394889
  likes: 3956
  last_modified: '2024-02-19'
  observed_at: '2026-11-20'
  source: https://huggingface.co/openai-community/gpt2
aliases:
- openai-community/gpt2
lineage:
  parents: []
  children: []
quotes:
  summary: version of GPT-2, with 124M parameters.
  intended_use: You can use the raw model for text generation or fine-tune it to a downstream task.
  what_sets_it_apart: was trained to guess the next word in sentences
tags:
- gpt2
updated: '2026-11-23'
sources:
- url: https://huggingface.co/openai-community/gpt2
  title: openai-community/gpt2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai-community/gpt2/blob/main/config.json
  title: openai-community/gpt2 — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gpt2/modeling_gpt2.py
  title: 'transformers/models/gpt2/modeling_gpt2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


