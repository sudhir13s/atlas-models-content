---
id: model/text-summarization
type: model
slug: text-summarization
schema_version: 1.1
title: text_summarization
organization: organization/falconsai
release_date: '2023-10-21'
release_year: 2023
release_month: 10
license: license/apache-2-0
model_category: language
tasks:
- task/summarization
parameters_active_b: 0.06
total_parameters: 60506624
context_window_tokens: 512
summary: text_summarization is a summarization model published on Hugging Face by Falconsai, not by the makers of the original T5 model. The card says it is a fine-tuned version of t5-small, a small T5 transformer, that writes concise summaries of input text. The card says it was fine-tuned on documents paired with human-written summaries, but does not name the dataset.
intended_use: The card names text summarization as the primary use, for lengthy documents, news articles and other text. It says performance may vary on other language tasks, and points users to other fine-tuned models for those.
what_sets_it_apart: The card describes fine-tuning with a batch size of 8 and a learning rate of 2e-5 on a variety of documents with human-generated summaries. It does not describe any other distinguishing feature.
fingerprint:
  attention_type: MHA
  num_layers: 6
  attention_heads: 8
  kv_heads: 8
  head_size: 64
  hidden_size: 512
  intermediate_size: 2048
  context_window_tokens: 512
  vocabulary_size: 32128
  activation_function: relu
  normalization: LayerNorm
  norm_placement: pre
  stack: encoder-decoder
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: summarization
  library: transformers
hub_signals:
  downloads_30d: 4351
  likes: 331
  last_modified: '2024-02-17'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Falconsai/text_summarization
aliases:
- Falconsai/text_summarization
lineage:
  parents: []
  children: []
quotes:
  summary: is a variant of the T5 transformer model, designed for the task of text summarization.
  intended_use: The primary intended use of this model is to generate concise and coherent text summaries.
  what_sets_it_apart: a batch size of 8 is chosen for efficient computation and learning.
tags:
- llm
- summarization
- t5
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Falconsai/text_summarization
  title: Falconsai/text_summarization — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Falconsai/text_summarization/blob/main/config.json
  title: Falconsai/text_summarization — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/t5/modeling_t5.py
  title: 'transformers/models/t5/modeling_t5.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

text_summarization is a summarization model published on Hugging Face by Falconsai, not by the makers of the original T5 model. The card says it is a fine-tuned version of t5-small, a small T5 transformer, that writes concise summaries of input text. The card says it was fine-tuned on documents paired with human-written summaries, but does not name the dataset.

## Intended Use

The card names text summarization as the primary use, for lengthy documents, news articles and other text. It says performance may vary on other language tasks, and points users to other fine-tuned models for those.

## What Sets It Apart

The card describes fine-tuning with a batch size of 8 and a learning rate of 2e-5 on a variety of documents with human-generated summaries. It does not describe any other distinguishing feature.
