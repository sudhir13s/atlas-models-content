---
id: model/llama-3-1-70b-instruct
type: model
slug: llama-3-1-70b-instruct
schema_version: 1.1
title: Llama-3.1-70B-Instruct
organization: organization/meta
release_date: '2024-07-16'
release_year: 2024
release_month: 7
license: license/llama-3-1
model_category: language
tasks:
- task/text-generation
total_parameters: 70553706496
base_models:
- name: meta-llama/Meta-Llama-3.1-70B
  address: https://huggingface.co/meta-llama/Meta-Llama-3.1-70B
summary: 'Llama 3.1 70B Instruct is the instruction-tuned 70-billion-parameter model of Meta''s Llama 3.1 family, which takes text in and returns text. The card says it is an auto-regressive language model with an optimized transformer architecture, supports a 128k-token context and eight languages: English, German, French, Italian, Portuguese, Hindi, Spanish and Thai.'
intended_use: Meta says the instruction-tuned text models are intended for assistant-like chat, in commercial and research use in the supported languages. The collection also supports using model outputs to improve other models, such as synthetic data generation and distillation. Out-of-scope uses are anything that breaks applicable laws or the Acceptable Use Policy, and languages beyond those listed unless a developer fine-tunes for them.
what_sets_it_apart: The card says the tuned versions use supervised fine-tuning and reinforcement learning with human feedback to align with human preferences for helpfulness and safety. It lists Grouped-Query Attention, a pretraining set of more than 15 trillion tokens from a new mix of publicly available online data, and a knowledge cutoff of December 2023.
filters:
  size_category: large
  is_gated: true
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 90193
  likes: 1032
  last_modified: '2024-12-15'
  observed_at: '2026-11-20'
  source: https://huggingface.co/meta-llama/Llama-3.1-70B-Instruct
aliases:
- meta-llama/Llama-3.1-70B-Instruct
- llama-3.1-70b-instruct
lineage:
  parents: []
  children: []
quotes:
  summary: Llama 3.1 is an auto-regressive language model that uses an optimized transformer architecture.
  intended_use: Instruction tuned text only models are intended for assistant-like chat, whereas pretrained models can be adapted for a variety of natural language generation tasks.
  what_sets_it_apart: The tuned versions use supervised fine-tuning (SFT) and reinforcement learning with human feedback (RLHF) to align with human preferences for helpfulness and safety.
tags:
- llm
- text-generation
updated: '2026-11-23'
sources:
- url: https://huggingface.co/meta-llama/Llama-3.1-70B-Instruct
  title: meta-llama/Llama-3.1-70B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2204.05149
  title: arXiv:2204.05149
  trust: 0.95
---

## Summary

Llama 3.1 70B Instruct is the instruction-tuned 70-billion-parameter model of Meta's Llama 3.1 family, which takes text in and returns text. The card says it is an auto-regressive language model with an optimized transformer architecture, supports a 128k-token context and eight languages: English, German, French, Italian, Portuguese, Hindi, Spanish and Thai.

## Intended Use

Meta says the instruction-tuned text models are intended for assistant-like chat, in commercial and research use in the supported languages. The collection also supports using model outputs to improve other models, such as synthetic data generation and distillation. Out-of-scope uses are anything that breaks applicable laws or the Acceptable Use Policy, and languages beyond those listed unless a developer fine-tunes for them.

## What Sets It Apart

The card says the tuned versions use supervised fine-tuning and reinforcement learning with human feedback to align with human preferences for helpfulness and safety. It lists Grouped-Query Attention, a pretraining set of more than 15 trillion tokens from a new mix of publicly available online data, and a knowledge cutoff of December 2023.
