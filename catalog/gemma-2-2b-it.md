---
id: model/gemma-2-2b-it
type: model
slug: gemma-2-2b-it
schema_version: 1.1
title: gemma-2-2b-it
organization: organization/google-deepmind
release_date: '2024-07-16'
release_year: 2024
release_month: 7
license: license/gemma
model_category: language
tasks:
- task/text-generation
total_parameters: 2614341888
base_models:
- name: google/gemma-2-2b
  address: https://huggingface.co/google/gemma-2-2b
summary: Gemma 2 2B IT is a text-generation model from Google, the instruction-tuned version of the 2B size of the Gemma 2 family. Instruction-tuned means it was trained further to follow requests and hold a conversation. The card describes Gemma models as text-to-text, decoder-only large language models, available in English, with open weights.
intended_use: 'The card lists content creation and communication: text generation such as poems, scripts, code, marketing copy and email drafts, chatbots and conversational AI, and text summarization. For research and education it lists natural language processing research, language learning tools and knowledge exploration. It says the list is not comprehensive and that its small size makes deployment possible on a laptop, a desktop or a user''s own cloud.'
what_sets_it_apart: The card says the Gemma family is built from the same research and technology used to create Google's Gemini models, and that open weights are released for both pre-trained and instruction-tuned variants.
filters:
  size_category: small
  is_gated: true
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 199893
  likes: 1623
  last_modified: '2024-08-27'
  observed_at: '2026-11-20'
  source: https://huggingface.co/google/gemma-2-2b-it
variants:
- name: bartowski/gemma-2-2b-it-GGUF
  format: gguf
  address: https://huggingface.co/bartowski/gemma-2-2b-it-GGUF
  publisher: bartowski
aliases:
- google/gemma-2-2b-it
- gemma-2-2b-it-gguf
lineage:
  parents: []
  children: []
quotes:
  summary: They are text-to-text, decoder-only large language models, available in English,
  intended_use: 'Chatbots and Conversational AI: Power conversational interfaces for customer'
  what_sets_it_apart: built from the same research and technology used to create the Gemini models.
tags:
- llm
- text-generation
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/gemma-2-2b-it
  title: google/gemma-2-2b-it — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2009.03300
  title: arXiv:2009.03300
  trust: 0.95
- url: https://arxiv.org/abs/1905.07830
  title: arXiv:1905.07830
  trust: 0.95
- url: https://arxiv.org/abs/1911.11641
  title: arXiv:1911.11641
  trust: 0.95
- url: https://arxiv.org/abs/1904.09728
  title: arXiv:1904.09728
  trust: 0.95
- url: https://arxiv.org/abs/1905.10044
  title: arXiv:1905.10044
  trust: 0.95
- url: https://arxiv.org/abs/1907.10641
  title: arXiv:1907.10641
  trust: 0.95
- url: https://arxiv.org/abs/1811.00937
  title: arXiv:1811.00937
  trust: 0.95
- url: https://arxiv.org/abs/1809.02789
  title: arXiv:1809.02789
  trust: 0.95
- url: https://arxiv.org/abs/1911.01547
  title: arXiv:1911.01547
  trust: 0.95
- url: https://arxiv.org/abs/1705.03551
  title: arXiv:1705.03551
  trust: 0.95
- url: https://arxiv.org/abs/2107.03374
  title: arXiv:2107.03374
  trust: 0.95
- url: https://arxiv.org/abs/2108.07732
  title: arXiv:2108.07732
  trust: 0.95
- url: https://arxiv.org/abs/2110.14168
  title: arXiv:2110.14168
  trust: 0.95
- url: https://arxiv.org/abs/2009.11462
  title: arXiv:2009.11462
  trust: 0.95
- url: https://arxiv.org/abs/2101.11718
  title: arXiv:2101.11718
  trust: 0.95
- url: https://arxiv.org/abs/2110.08193
  title: arXiv:2110.08193
  trust: 0.95
- url: https://arxiv.org/abs/1804.09301
  title: arXiv:1804.09301
  trust: 0.95
- url: https://arxiv.org/abs/2109.07958
  title: arXiv:2109.07958
  trust: 0.95
- url: https://arxiv.org/abs/1804.06876
  title: arXiv:1804.06876
  trust: 0.95
- url: https://arxiv.org/abs/2103.03874
  title: arXiv:2103.03874
  trust: 0.95
- url: https://arxiv.org/abs/2304.06364
  title: arXiv:2304.06364
  trust: 0.95
- url: https://arxiv.org/abs/1903.00161
  title: arXiv:1903.00161
  trust: 0.95
- url: https://arxiv.org/abs/2206.04615
  title: arXiv:2206.04615
  trust: 0.95
- url: https://arxiv.org/abs/2203.09509
  title: arXiv:2203.09509
  trust: 0.95
- url: https://arxiv.org/abs/2403.13793
  title: arXiv:2403.13793
  trust: 0.95
---

## Summary

Gemma 2 2B IT is a text-generation model from Google, the instruction-tuned version of the 2B size of the Gemma 2 family. Instruction-tuned means it was trained further to follow requests and hold a conversation. The card describes Gemma models as text-to-text, decoder-only large language models, available in English, with open weights.

## Intended Use

The card lists content creation and communication: text generation such as poems, scripts, code, marketing copy and email drafts, chatbots and conversational AI, and text summarization. For research and education it lists natural language processing research, language learning tools and knowledge exploration. It says the list is not comprehensive and that its small size makes deployment possible on a laptop, a desktop or a user's own cloud.

## What Sets It Apart

The card says the Gemma family is built from the same research and technology used to create Google's Gemini models, and that open weights are released for both pre-trained and instruction-tuned variants.
