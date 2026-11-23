---
id: model/gemma-2-27b-it
type: model
slug: gemma-2-27b-it
schema_version: 1.1
title: gemma-2-27b-it
organization: organization/google-deepmind
release_date: '2024-06-24'
release_year: 2024
release_month: 6
license: license/gemma
model_category: language
tasks:
- task/text-generation
total_parameters: 27227128320
base_models:
- name: google/gemma-2-27b
  address: https://huggingface.co/google/gemma-2-27b
summary: Gemma 2 27B IT is the instruction-tuned 27-billion-parameter model of Google's Gemma 2 family, which is built from the same research and technology as Google's Gemini models. The card describes Gemma as text-to-text, decoder-only language models, available in English, with open weights for both pre-trained and instruction-tuned variants.
intended_use: The card lists text generation such as poems, scripts, code, marketing copy and email drafts, chatbots and conversational AI, and text summarization. For research and education it lists natural language processing research, language-learning tools and exploring large bodies of text. It says the models are suited to question answering, summarization and reasoning, and small enough to run on a laptop, a desktop or your own cloud.
limitations: The card says the quality and diversity of the training data shape the model's capabilities, so biases or gaps in the data can limit its responses, and the scope of the training data decides which subject areas it handles well.
filters:
  size_category: large
  is_gated: true
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 22229
  likes: 579
  last_modified: '2024-08-27'
  observed_at: '2026-11-20'
  source: https://huggingface.co/google/gemma-2-27b-it
aliases:
- google/gemma-2-27b-it
lineage:
  parents: []
  children: []
quotes:
  summary: They are text-to-text, decoder-only large language models, available in English,
  intended_use: 'Text Generation: These models can be used to generate creative text formats such as poems, scripts, code, marketing copy, and email drafts.'
  limitations: The scope of the training dataset determines the subject areas the model can handle effectively.
tags:
- llm
- text-generation
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/gemma-2-27b-it
  title: google/gemma-2-27b-it — model page (Hugging Face)
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
- url: https://arxiv.org/abs/2206.04615
  title: arXiv:2206.04615
  trust: 0.95
- url: https://arxiv.org/abs/2203.09509
  title: arXiv:2203.09509
  trust: 0.95
---

## Summary

Gemma 2 27B IT is the instruction-tuned 27-billion-parameter model of Google's Gemma 2 family, which is built from the same research and technology as Google's Gemini models. The card describes Gemma as text-to-text, decoder-only language models, available in English, with open weights for both pre-trained and instruction-tuned variants.

## Intended Use

The card lists text generation such as poems, scripts, code, marketing copy and email drafts, chatbots and conversational AI, and text summarization. For research and education it lists natural language processing research, language-learning tools and exploring large bodies of text. It says the models are suited to question answering, summarization and reasoning, and small enough to run on a laptop, a desktop or your own cloud.

## Limitations

The card says the quality and diversity of the training data shape the model's capabilities, so biases or gaps in the data can limit its responses, and the scope of the training data decides which subject areas it handles well.
