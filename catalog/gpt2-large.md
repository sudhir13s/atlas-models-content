---
id: model/gpt2-large
type: model
slug: gpt2-large
schema_version: 1.1
title: gpt2-large
organization: organization/openai
license: license/mit
architecture_id: architecture-era/gpt
model_category: language
tasks:
- task/text-generation
parameters_active_b: 0.81
total_parameters: 811778816
context_window_tokens: 1024
summary: GPT-2 Large is the 774 million parameter version of GPT-2, a transformer-based language model created and released by OpenAI. It was pretrained on English text with a causal language modeling objective, which means it learns to predict the next word from the words before it, so it can continue a piece of text.
intended_use: Quoting OpenAI's own GPT-2 model card, the card names AI researchers and practitioners as the primary users, who are expected to study the behaviors, capabilities, biases and constraints of large generative language models. Likely secondary uses are writing assistance, creative writing and entertainment such as chat bots. Out of scope are uses that need the generated text to be true, and deployment in systems that interact with people unless the deployers first study the biases relevant to their use.
what_sets_it_apart: The card places it as one size in the GPT-2 family and lists GPT-2, GPT-Medium and GPT-XL as related models. OpenAI's quoted text says it found no statistically significant difference in gender, race and religious bias probes between the 774M and 1.5B versions, so all versions should be approached with similar caution.
limitations: The card says the training data has not been released as a browsable dataset and is known to contain a lot of unfiltered internet content. It warns that predictions can include harmful stereotypes across protected classes, identity characteristics, and social and occupational groups.
fingerprint:
  attention_type: MHA
  num_layers: 36
  attention_heads: 20
  kv_heads: 20
  head_size: 64
  hidden_size: 1280
  context_window_tokens: 1024
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
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 1096089
  likes: 375
  last_modified: '2024-02-19'
  observed_at: '2026-11-20'
  source: https://huggingface.co/openai-community/gpt2-large
aliases:
- openai-community/gpt2-large
lineage:
  parents: []
  children: []
quotes:
  summary: GPT-2 Large is the **774M parameter** version of GPT-2, a transformer-based language model created and released by OpenAI.
  intended_use: The primary intended users of these models are AI researchers and practitioners.
  what_sets_it_apart: We found no statistically significant difference in gender, race, and religious bias probes between 774M and 1.5B
  limitations: We know it contains a lot of unfiltered content from the internet, which is far from neutral.
tags:
- llm
- text-generation
- gpt2
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/openai-community/gpt2-large
  title: openai-community/gpt2-large — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai-community/gpt2-large/blob/main/config.json
  title: openai-community/gpt2-large — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1910.09700
  title: arXiv:1910.09700
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gpt2/modeling_gpt2.py
  title: 'transformers/models/gpt2/modeling_gpt2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

GPT-2 Large is the 774 million parameter version of GPT-2, a transformer-based language model created and released by OpenAI. It was pretrained on English text with a causal language modeling objective, which means it learns to predict the next word from the words before it, so it can continue a piece of text.

## Intended Use

Quoting OpenAI's own GPT-2 model card, the card names AI researchers and practitioners as the primary users, who are expected to study the behaviors, capabilities, biases and constraints of large generative language models. Likely secondary uses are writing assistance, creative writing and entertainment such as chat bots. Out of scope are uses that need the generated text to be true, and deployment in systems that interact with people unless the deployers first study the biases relevant to their use.

## What Sets It Apart

The card places it as one size in the GPT-2 family and lists GPT-2, GPT-Medium and GPT-XL as related models. OpenAI's quoted text says it found no statistically significant difference in gender, race and religious bias probes between the 774M and 1.5B versions, so all versions should be approached with similar caution.

## Limitations

The card says the training data has not been released as a browsable dataset and is known to contain a lot of unfiltered internet content. It warns that predictions can include harmful stereotypes across protected classes, identity characteristics, and social and occupational groups.
