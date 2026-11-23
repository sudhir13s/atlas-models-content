---
id: model/bert-base-ner
type: model
slug: bert-base-ner
schema_version: 3.0
title: bert-base-NER
organization: organization/dslim
license: license/mit
model_category: language
model_kind: transformer-language
tasks:
- task/token-classification
parameters_active_b: 0.11
total_parameters: 108317193
context_window_tokens: 512
summary: 'bert-base-NER is a version of the BERT language model (the cased base size) fine-tuned by its publisher, dslim, for named entity recognition: finding names in text and labelling each as a person, organization, location or miscellaneous. The card says it was fine-tuned on the English CoNLL-2003 dataset, which is built from Reuters news stories.'
intended_use: 'The card presents it for named entity recognition on English text, used through the Transformers pipeline for token labelling. It marks four entity types: person, organization, location and miscellaneous.'
what_sets_it_apart: 'The model separates the start of an entity from its continuation, so two names of the same type that sit side by side can be told apart. The card also lists related versions fine-tuned the same way: a larger bert-large-NER, a smaller distilbert-NER and an uncased bert-base variant.'
limitations: The card says the model is limited by its training data, news articles from a specific span of time, so it may not generalize to other domains. It also notes that the model occasionally tags pieces of words as entities, so results may need post-processing.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  vocabulary_size: 28996
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: post
  stack: encoder
  embedding_norm: true
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: token-classification
  library: transformers
hub_signals:
  downloads_30d: 921050
  likes: 752
  last_modified: '2024-10-08'
  observed_at: '2026-11-21'
  source: https://huggingface.co/dslim/bert-base-NER
aliases:
- dslim/bert-base-NER
lineage:
  parents: []
  children: []
quotes:
  summary: 'It has been trained to recognize four types of entities: location (LOC), organizations (ORG), person (PER) and Miscellaneous (MISC).'
  intended_use: 'As in the dataset, each token will be classified as one of the following classes:'
  what_sets_it_apart: The training dataset distinguishes between the beginning and continuation of an entity so that if there are back-to-back entities of the same type, the model can output where the second entity begins.
  limitations: This model is limited by its training dataset of entity-annotated news articles from a specific span of time.
tags:
- bert
updated: '2026-11-23'
sources:
- url: https://huggingface.co/dslim/bert-base-NER
  title: dslim/bert-base-NER — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/dslim/bert-base-NER/blob/main/config.json
  title: dslim/bert-base-NER — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1810.04805
  title: arXiv:1810.04805
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bert/modeling_bert.py
  title: 'transformers/models/bert/modeling_bert.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


