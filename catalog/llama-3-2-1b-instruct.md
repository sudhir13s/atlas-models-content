---
id: model/llama-3-2-1b-instruct
type: model
slug: llama-3-2-1b-instruct
schema_version: 1.1
title: Llama-3.2-1B-Instruct
organization: organization/meta
release_date: '2024-09-18'
release_year: 2024
release_month: 9
license: license/llama-3-2
model_category: language
tasks:
- task/text-generation
total_parameters: 1235814400
filters:
  size_category: small
  is_gated: true
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 1308315
  likes: 2003
  last_modified: '2024-10-24'
  observed_at: '2026-11-19'
  source: https://huggingface.co/meta-llama/Llama-3.2-1B-Instruct
variants:
- name: RedHatAI/Llama-3.2-1B-Instruct-FP8
  format: fp8
  address: https://huggingface.co/RedHatAI/Llama-3.2-1B-Instruct-FP8
  publisher: RedHatAI
- name: RedHatAI/Llama-3.2-1B-Instruct-FP8-dynamic
  format: fp8
  address: https://huggingface.co/RedHatAI/Llama-3.2-1B-Instruct-FP8-dynamic
  publisher: RedHatAI
aliases:
- meta-llama/Llama-3.2-1B-Instruct
- llama-3.2-1b-instruct
- llama-3.2-1b-instruct-fp8
- llama-3.2-1b-instruct-fp8-dynamic
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
updated: '2026-11-19'
sources:
- url: https://huggingface.co/meta-llama/Llama-3.2-1B-Instruct
  title: meta-llama/Llama-3.2-1B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2204.05149
  title: arXiv:2204.05149
  trust: 0.95
- url: https://arxiv.org/abs/2405.16406
  title: arXiv:2405.16406
  trust: 0.95
---


