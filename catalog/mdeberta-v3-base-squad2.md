---
id: model/mdeberta-v3-base-squad2
type: model
slug: mdeberta-v3-base-squad2
schema_version: 1.1
title: mdeberta-v3-base-squad2
organization: organization/timpal0l
release_date: '2022-11-30'
release_year: 2022
release_month: 11
license: license/mit
model_category: language
tasks:
- task/question-answering
parameters_active_b: 0.28
total_parameters: 278220802
context_window_tokens: 512
base_models:
- name: microsoft/mdeberta-v3-base
  address: https://huggingface.co/microsoft/mdeberta-v3-base
summary: 'mdeberta-v3-base-squad2 is an extractive question-answering model: given a question and a passage, it returns the stretch of the passage that answers the question. It was published on Hugging Face by timpal0l, not by the makers of the underlying model, and the card says it was fine-tuned for 3 epochs on SQuAD2.0, a question-answering dataset. Its base model is microsoft/mdeberta-v3-base, a multilingual DeBERTa model.'
intended_use: The card says the model can be used for extractive question answering, with a short example that asks where someone lives and gets back the answer span from a given sentence.
what_sets_it_apart: The card describes mDeBERTa as the multilingual version of DeBERTa, with the same structure and trained on CC100 multilingual data. DeBERTaV3, the version behind it, is pre-trained with an ELECTRA-style method and gradient-disentangled embedding sharing. The card does not report any distinguishing change made by this fine-tune beyond training on SQuAD2.0.
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
  norm_placement: post
  stack: encoder
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: question-answering
  library: transformers
hub_signals:
  downloads_30d: 6423
  likes: 260
  last_modified: '2024-11-26'
  observed_at: '2026-11-21'
  source: https://huggingface.co/timpal0l/mdeberta-v3-base-squad2
aliases:
- timpal0l/mdeberta-v3-base-squad2
lineage:
  parents: []
  children: []
quotes:
  summary: It has been finetuned for 3 epochs on [SQuAD2.0](https://rajpurkar.github.io/SQuAD-explorer/).
  intended_use: This model can be used for Extractive QA
  what_sets_it_apart: mDeBERTa is multilingual version of DeBERTa which use the same structure as DeBERTa and was trained with CC100 multilingual data.
tags:
- llm
- question-answering
- deberta-v2
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/timpal0l/mdeberta-v3-base-squad2
  title: timpal0l/mdeberta-v3-base-squad2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/timpal0l/mdeberta-v3-base-squad2/blob/main/config.json
  title: timpal0l/mdeberta-v3-base-squad2 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2006.03654
  title: arXiv:2006.03654
  trust: 0.95
- url: https://arxiv.org/abs/2111.09543
  title: arXiv:2111.09543
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/deberta_v2/modeling_deberta_v2.py
  title: 'transformers/models/deberta_v2/modeling_deberta_v2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

mdeberta-v3-base-squad2 is an extractive question-answering model: given a question and a passage, it returns the stretch of the passage that answers the question. It was published on Hugging Face by timpal0l, not by the makers of the underlying model, and the card says it was fine-tuned for 3 epochs on SQuAD2.0, a question-answering dataset. Its base model is microsoft/mdeberta-v3-base, a multilingual DeBERTa model.

## Intended Use

The card says the model can be used for extractive question answering, with a short example that asks where someone lives and gets back the answer span from a given sentence.

## What Sets It Apart

The card describes mDeBERTa as the multilingual version of DeBERTa, with the same structure and trained on CC100 multilingual data. DeBERTaV3, the version behind it, is pre-trained with an ELECTRA-style method and gradient-disentangled embedding sharing. The card does not report any distinguishing change made by this fine-tune beyond training on SQuAD2.0.
