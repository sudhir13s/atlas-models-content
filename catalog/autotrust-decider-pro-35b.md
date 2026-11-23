---
id: model/autotrust-decider-pro-35b
type: model
slug: autotrust-decider-pro-35b
schema_version: 1.1
title: AutoTrust-Decider-Pro-35B
organization: organization/autotrust
release_date: '2026-10-11'
release_year: 2026
release_month: 10
license: license/apache-2-0
model_category: language
tasks:
- task/text-classification
total_parameters: 35951822704
context_window_tokens: 262144
base_models:
- name: Qwen/Qwen3.5-35B-A3B
  address: https://huggingface.co/Qwen/Qwen3.5-35B-A3B
summary: 'AutoTrust-Decider-Pro-35B is an open-weights decision model from AutoTrust AI, fine-tuned from Qwen3.5-35B-A3B (its card is titled JEV-35B). It does not write text. It reads a situation and a question and returns probabilities over a fixed set of answers: yes or no, a score from 0 to 5, or a choice among up to 256 listed options. The card describes a mixture-of-experts model with 35 billion parameters, about 3 billion of them active per token.'
intended_use: The card links a blog post titled One Decision Model Instead of a Ranking Team and shows the model making calibrated decisions such as ranking news headlines, re-ranking search results, routing a support ticket to a team, or choosing the next click for a computer-use agent. The card says it answers each decision in a single pass and that it is not for chat or text generation.
what_sets_it_apart: Instead of generating words, the model reads the internal state at the last token of a fixed prompt and passes it through a decision head, a 264-slot output layer merged into the language-model head, to get one score per answer slot. These scores become calibrated probabilities. The card says its decision training used text only, with no robot or embodied data.
limitations: 'The card lists these limits: it is a decision model only, with no chat, text generation or thinking mode of its own; knowledge and reasoning are its weak area; it is weaker than the maker''s JEV-27B-VL model at sequential game play; and it is English-centric. The card advises against using it for high-stakes decisions without confidence gating.'
fingerprint:
  attention_type: GQA
  num_layers: 40
  attention_heads: 16
  kv_heads: 2
  head_size: 256
  hidden_size: 2048
  context_window_tokens: 262144
  vocabulary_size: 248320
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000000.0
moe_config:
  expert_count: 256
  active_experts: 8
  shared_expert: true
filters:
  size_category: large
  is_quantized: false
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-classification
  library: transformers
hub_signals:
  downloads_30d: 927364
  likes: 4351
  last_modified: '2026-10-14'
  observed_at: '2026-11-21'
  source: https://huggingface.co/autotrust/AutoTrust-Decider-Pro-35B
aliases:
- autotrust/AutoTrust-Decider-Pro-35B
lineage:
  parents: []
  children: []
quotes:
  summary: A System 1 decision model on Qwen3.5-35B-A3B (35 B parameters, about 3 B active per token), up to 256 options in one pass
  intended_use: 'JEV-35B is a decision model only: it does not chat or generate text.'
  what_sets_it_apart: A 264-slot decision head gives one logit per slot
  limitations: English-centric; not for high-stakes decisions without confidence gating.
tags:
- llm
- text-classification
- qwen3-5-moe
- moe
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/autotrust/AutoTrust-Decider-Pro-35B
  title: autotrust/AutoTrust-Decider-Pro-35B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/autotrust/AutoTrust-Decider-Pro-35B/blob/main/config.json
  title: autotrust/AutoTrust-Decider-Pro-35B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2609.14973
  title: arXiv:2609.14973
  trust: 0.95
---

## Summary

AutoTrust-Decider-Pro-35B is an open-weights decision model from AutoTrust AI, fine-tuned from Qwen3.5-35B-A3B (its card is titled JEV-35B). It does not write text. It reads a situation and a question and returns probabilities over a fixed set of answers: yes or no, a score from 0 to 5, or a choice among up to 256 listed options. The card describes a mixture-of-experts model with 35 billion parameters, about 3 billion of them active per token.

## Intended Use

The card links a blog post titled One Decision Model Instead of a Ranking Team and shows the model making calibrated decisions such as ranking news headlines, re-ranking search results, routing a support ticket to a team, or choosing the next click for a computer-use agent. The card says it answers each decision in a single pass and that it is not for chat or text generation.

## What Sets It Apart

Instead of generating words, the model reads the internal state at the last token of a fixed prompt and passes it through a decision head, a 264-slot output layer merged into the language-model head, to get one score per answer slot. These scores become calibrated probabilities. The card says its decision training used text only, with no robot or embodied data.

## Limitations

The card lists these limits: it is a decision model only, with no chat, text generation or thinking mode of its own; knowledge and reasoning are its weak area; it is weaker than the maker's JEV-27B-VL model at sequential game play; and it is English-centric. The card advises against using it for high-stakes decisions without confidence gating.
