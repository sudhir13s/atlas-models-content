---
id: model/mdeberta-v3-base-xnli-multilingual-nli-2mil7
type: model
slug: mdeberta-v3-base-xnli-multilingual-nli-2mil7
schema_version: 1.1
title: mDeBERTa-v3-base-xnli-multilingual-nli-2mil7
organization: organization/moritzlaurer
release_date: '2022-08-22'
release_year: 2022
release_month: 8
license: license/mit
model_category: language
tasks:
- task/zero-shot-classification
parameters_active_b: 0.28
total_parameters: 278812163
context_window_tokens: 512
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 512
  vocabulary_size: 251000
  activation_function: gelu
  normalization: LayerNorm
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: zero-shot-classification
  library: transformers
hub_signals:
  downloads_30d: 758679
  likes: 412
  last_modified: '2024-04-11'
  observed_at: '2026-11-21'
  source: https://huggingface.co/MoritzLaurer/mDeBERTa-v3-base-xnli-multilingual-nli-2mil7
aliases:
- MoritzLaurer/mDeBERTa-v3-base-xnli-multilingual-nli-2mil7
lineage:
  parents: []
  children: []
tags:
- llm
- zero-shot-classification
- deberta-v2
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/MoritzLaurer/mDeBERTa-v3-base-xnli-multilingual-nli-2mil7
  title: MoritzLaurer/mDeBERTa-v3-base-xnli-multilingual-nli-2mil7 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/MoritzLaurer/mDeBERTa-v3-base-xnli-multilingual-nli-2mil7/blob/main/config.json
  title: MoritzLaurer/mDeBERTa-v3-base-xnli-multilingual-nli-2mil7 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2111.09543
  title: arXiv:2111.09543
  trust: 0.95
- url: https://arxiv.org/abs/2104.07179
  title: arXiv:2104.07179
  trust: 0.95
- url: https://arxiv.org/abs/1809.05053
  title: arXiv:1809.05053
  trust: 0.95
- url: https://arxiv.org/abs/1911.02116
  title: arXiv:1911.02116
  trust: 0.95
---


