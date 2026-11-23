---
id: model/nllb-200-distilled-600m
type: model
slug: nllb-200-distilled-600m
schema_version: 3.0
title: nllb-200-distilled-600M
organization: organization/meta
release_date: '2022-07-08'
license_note: cc-by-nc-4.0
model_category: language
model_kind: transformer-language
tasks:
- task/translation
context_window_tokens: 1024
summary: NLLB-200 distilled 600M is a machine translation model from the NLLB (No Language Left Behind) team that translates single sentences among 200 languages. This card is for the distilled 600M variant, the smaller version of the NLLB-200 model. The card says it is aimed mainly at research, especially for low-resource languages, which are languages with little training text available.
intended_use: 'The card lists researchers and the machine translation research community as the primary users. It marks several uses as out of scope: production deployment, domain-specific text such as medical or legal, document translation, and certified translations. It adds that the model was trained on inputs of up to 512 tokens, so longer sequences may translate worse.'
what_sets_it_apart: The card describes training on parallel multilingual data from a variety of sources, plus monolingual data built from Common Crawl, and refers to the paper for the strategies used for data imbalance between high and low-resource languages. It notes that many of the chosen languages are low-resource, with a heavy emphasis on African languages.
fingerprint:
  attention_type: MHA
  num_layers: 12
  decoder_layers: 12
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  intermediate_size: 4096
  vocabulary_size: 256206
  activation_function: relu
  normalization: LayerNorm
  norm_placement: pre
  stack: encoder-decoder
  positional_encoding: Sinusoidal
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: translation
  library: transformers
hub_signals:
  downloads_30d: 765198
  likes: 1017
  last_modified: '2024-02-14'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/nllb-200-distilled-600M
aliases:
- facebook/nllb-200-distilled-600M
lineage:
  parents: []
  children: []
quotes:
  summary: NLLB-200 is a machine translation model primarily intended for research in machine translation
  intended_use: NLLB-200 is a research model and is not released for production deployment.
  what_sets_it_apart: many languages chosen for this study are low-resource languages, with a heavy emphasis on African languages.
tags:
- m2m-100
updated: '2026-11-23'
sources:
- url: https://huggingface.co/facebook/nllb-200-distilled-600M
  title: facebook/nllb-200-distilled-600M — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/nllb-200-distilled-600M/blob/main/config.json
  title: facebook/nllb-200-distilled-600M — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/m2m_100/modeling_m2m_100.py
  title: 'transformers/models/m2m_100/modeling_m2m_100.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


