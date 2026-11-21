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
tags:
- llm
- summarization
- t5
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/Falconsai/text_summarization
  title: Falconsai/text_summarization — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Falconsai/text_summarization/blob/main/config.json
  title: Falconsai/text_summarization — config.json
  trust: 0.9
---


