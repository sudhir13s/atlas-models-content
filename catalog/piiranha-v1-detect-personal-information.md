---
id: model/piiranha-v1-detect-personal-information
type: model
slug: piiranha-v1-detect-personal-information
schema_version: 1.1
title: piiranha-v1-detect-personal-information
organization: organization/iiiorg
release_date: '2024-09-12'
release_year: 2024
release_month: 9
license_note: cc-by-nc-nd-4.0
model_category: language
tasks:
- task/token-classification
parameters_active_b: 0.28
total_parameters: 278232594
context_window_tokens: 512
base_models:
- name: microsoft/mdeberta-v3-base
  address: https://huggingface.co/microsoft/mdeberta-v3-base
summary: 'Piiranha-v1 is a model that finds personally identifiable information (PII), such as names, emails and card numbers, in text. It was published on Hugging Face by iiiorg and is a fine-tuned version of microsoft/mdeberta-v3-base. The card says it detects 17 types of PII across six languages: English, Spanish, French, German, Italian and Dutch.'
intended_use: The card says Piiranha can be used to assist with redacting PII from texts, and adds that it is used at your own risk, with no responsibility accepted for incorrect predictions.
what_sets_it_apart: The card lists the 17 supported PII types, among them account number, credit card number, date of birth, email, password, phone number and username. It notes that the model sometimes confuses one PII type for another, such as first names for last names, but still flags the token as PII, which is what matters for redaction.
running_it: The card gives a context length of 256 DeBERTa tokens and says to split longer text.
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
  pipeline_tag: token-classification
  library: transformers
hub_signals:
  downloads_30d: 202621
  likes: 250
  last_modified: '2025-11-01'
  observed_at: '2026-11-21'
  source: https://huggingface.co/iiiorg/piiranha-v1-detect-personal-information
aliases:
- iiiorg/piiranha-v1-detect-personal-information
lineage:
  parents: []
  children: []
quotes:
  summary: Piiranha is a fine-tuned version of [microsoft/mdeberta-v3-base](https://huggingface.co/microsoft/mdeberta-v3-base).
  intended_use: Piiranha can be used to assist with redacting PII from texts.
  what_sets_it_apart: The model sometimes misclassifies one PII type for another, but at the end of the day, it still recognizes the token as PII.
  running_it: The context length is 256 Deberta tokens. If your text is longer than that, just split it up.
tags:
- llm
- token-classification
- deberta-v2
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/iiiorg/piiranha-v1-detect-personal-information
  title: iiiorg/piiranha-v1-detect-personal-information — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/iiiorg/piiranha-v1-detect-personal-information/blob/main/config.json
  title: iiiorg/piiranha-v1-detect-personal-information — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/deberta_v2/modeling_deberta_v2.py
  title: 'transformers/models/deberta_v2/modeling_deberta_v2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Piiranha-v1 is a model that finds personally identifiable information (PII), such as names, emails and card numbers, in text. It was published on Hugging Face by iiiorg and is a fine-tuned version of microsoft/mdeberta-v3-base. The card says it detects 17 types of PII across six languages: English, Spanish, French, German, Italian and Dutch.

## Intended Use

The card says Piiranha can be used to assist with redacting PII from texts, and adds that it is used at your own risk, with no responsibility accepted for incorrect predictions.

## What Sets It Apart

The card lists the 17 supported PII types, among them account number, credit card number, date of birth, email, password, phone number and username. It notes that the model sometimes confuses one PII type for another, such as first names for last names, but still flags the token as PII, which is what matters for redaction.

## Running It

The card gives a context length of 256 DeBERTa tokens and says to split longer text.
