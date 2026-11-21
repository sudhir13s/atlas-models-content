---
id: model/distilbart-cnn-12-6
type: model
slug: distilbart-cnn-12-6
schema_version: 1.1
title: distilbart-cnn-12-6
organization: organization/sshleifer
license: license/apache-2-0
model_category: language
tasks:
- task/summarization
context_window_tokens: 1024
fingerprint:
  num_layers: 12
  hidden_size: 1024
  context_window_tokens: 1024
  vocabulary_size: 50264
  activation_function: gelu
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: summarization
  library: transformers
hub_signals:
  downloads_30d: 276369
  likes: 329
  last_modified: '2021-06-14'
  observed_at: '2026-11-21'
  source: https://huggingface.co/sshleifer/distilbart-cnn-12-6
aliases:
- sshleifer/distilbart-cnn-12-6
lineage:
  parents: []
  children: []
tags:
- llm
- summarization
- bart
- dense
updated: '2026-11-21'
sources:
- url: https://huggingface.co/sshleifer/distilbart-cnn-12-6
  title: sshleifer/distilbart-cnn-12-6 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/sshleifer/distilbart-cnn-12-6/blob/main/config.json
  title: sshleifer/distilbart-cnn-12-6 — config.json
  trust: 0.9
---


