---
id: model/llama-3-2-3b-instruct
type: model
slug: llama-3-2-3b-instruct
schema_version: 1.1
title: Llama-3.2-3B-Instruct
organization: organization/meta
release_date: '2024-09-18'
release_year: 2024
release_month: 9
license: license/llama-3-2
model_category: language
tasks:
- task/text-generation
total_parameters: 3212749824
summary: 'Llama 3.2 3B Instruct is an instruction-tuned text model from Meta, part of the Llama 3.2 collection of multilingual language models in 1B and 3B sizes (text in, text out). The card lists 3.21 billion parameters and a 128k-token context length. Eight languages are officially supported: English, German, French, Italian, Portuguese, Hindi, Spanish and Thai.'
intended_use: Meta says Llama 3.2 is intended for commercial and research use in multiple languages. The instruction-tuned text models are meant for assistant-like chat and agentic applications such as knowledge retrieval and summarization, mobile writing assistants, and query and prompt rewriting. Out of scope are uses that break applicable laws, uses prohibited by the Acceptable Use Policy and license, and languages beyond those the card lists as supported.
what_sets_it_apart: Meta says that for the 1B and 3B models it fed outputs (logits) from the larger Llama 3.1 8B and 70B models into pretraining as token-level targets, and used knowledge distillation after pruning to recover performance. Pretraining used up to 9 trillion tokens with a knowledge cutoff of December 2023. Post-training ran several rounds of supervised fine-tuning, rejection sampling and direct preference optimization.
running_it: The card says the repository holds two versions, one for the Transformers library and one for the original llama codebase. The Transformers example needs version 4.43.0 or later and runs in bfloat16 precision.
filters:
  size_category: small
  is_gated: true
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 795292
  likes: 3054
  last_modified: '2024-10-24'
  observed_at: '2026-11-20'
  source: https://huggingface.co/meta-llama/Llama-3.2-3B-Instruct
aliases:
- meta-llama/Llama-3.2-3B-Instruct
- llama-3.2-3b-instruct
lineage:
  parents: []
  children: []
quotes:
  summary: The Llama 3.2 collection of multilingual large language models (LLMs) is a collection of pretrained and instruction-tuned generative models in 1B and 3B sizes (text in/text out).
  intended_use: Llama 3.2 is intended for commercial and research use in multiple languages.
  what_sets_it_apart: Knowledge distillation was used after pruning to recover performance.
  running_it: This repository contains two versions of Llama-3.2-3B-Instruct, for use with transformers and with the original llama codebase.
tags:
- llm
- text-generation
updated: '2026-11-23'
sources:
- url: https://huggingface.co/meta-llama/Llama-3.2-3B-Instruct
  title: meta-llama/Llama-3.2-3B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2204.05149
  title: arXiv:2204.05149
  trust: 0.95
- url: https://arxiv.org/abs/2405.16406
  title: arXiv:2405.16406
  trust: 0.95
---

## Summary

Llama 3.2 3B Instruct is an instruction-tuned text model from Meta, part of the Llama 3.2 collection of multilingual language models in 1B and 3B sizes (text in, text out). The card lists 3.21 billion parameters and a 128k-token context length. Eight languages are officially supported: English, German, French, Italian, Portuguese, Hindi, Spanish and Thai.

## Intended Use

Meta says Llama 3.2 is intended for commercial and research use in multiple languages. The instruction-tuned text models are meant for assistant-like chat and agentic applications such as knowledge retrieval and summarization, mobile writing assistants, and query and prompt rewriting. Out of scope are uses that break applicable laws, uses prohibited by the Acceptable Use Policy and license, and languages beyond those the card lists as supported.

## What Sets It Apart

Meta says that for the 1B and 3B models it fed outputs (logits) from the larger Llama 3.1 8B and 70B models into pretraining as token-level targets, and used knowledge distillation after pruning to recover performance. Pretraining used up to 9 trillion tokens with a knowledge cutoff of December 2023. Post-training ran several rounds of supervised fine-tuning, rejection sampling and direct preference optimization.

## Running It

The card says the repository holds two versions, one for the Transformers library and one for the original llama codebase. The Transformers example needs version 4.43.0 or later and runs in bfloat16 precision.
