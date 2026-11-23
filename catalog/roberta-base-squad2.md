---
id: model/roberta-base-squad2
type: model
slug: roberta-base-squad2
schema_version: 3.0
title: roberta-base-squad2
organization: organization/deepset
license_note: cc-by-4.0
model_category: language
model_kind: transformer-language
tasks:
- task/question-answering
parameters_active_b: 0.12
total_parameters: 124057092
context_window_tokens: 512
base_models:
- name: FacebookAI/roberta-base
  address: https://huggingface.co/FacebookAI/roberta-base
summary: 'roberta-base-squad2 is an extractive question answering model from deepset: given a question and a passage, it finds the span of the passage that answers the question. It is the roberta-base language model fine-tuned on the SQuAD2.0 dataset, which includes questions that have no answer in the passage. The card marks the language as English.'
intended_use: The card names extractive question answering as the task and describes using the model in the Haystack framework to answer questions over documents. Extractive means the answer is copied from the passage rather than written freshly. It also mentions a distilled version, deepset/tinyroberta-squad2, which it says has comparable prediction quality and runs at twice the speed.
what_sets_it_apart: The card says the model was trained on question and answer pairs that include unanswerable questions, so it is meant to handle passages that do not contain the answer. Training and evaluation both used SQuAD 2.0, on 4 Tesla V100 GPUs.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  vocabulary_size: 50265
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
  pipeline_tag: question-answering
  library: transformers
hub_signals:
  downloads_30d: 351798
  likes: 979
  last_modified: '2024-09-24'
  observed_at: '2026-11-21'
  source: https://huggingface.co/deepset/roberta-base-squad2
aliases:
- deepset/roberta-base-squad2
lineage:
  parents: []
  children: []
quotes:
  summary: It's been trained on question-answer pairs
  intended_use: You can use this model in Haystack to do extractive question answering on documents.
  what_sets_it_apart: including unanswerable questions, for the task of Extractive Question Answering
locked:
- context_window_tokens
tags:
- roberta
updated: '2026-11-23'
sources:
- url: https://huggingface.co/deepset/roberta-base-squad2
  title: deepset/roberta-base-squad2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepset/roberta-base-squad2/blob/main/config.json
  title: deepset/roberta-base-squad2 — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/roberta/modeling_roberta.py
  title: 'transformers/models/roberta/modeling_roberta.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


