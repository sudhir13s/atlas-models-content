---
id: model/llama-2-7b-hf
type: model
slug: llama-2-7b-hf
schema_version: 1.1
title: Llama-2-7b-hf
organization: organization/meta
release_date: '2023-07-13'
release_year: 2023
release_month: 7
license: license/llama-2
model_category: language
tasks:
- task/text-generation
total_parameters: 6738417664
summary: Llama 2 7B is the 7-billion-parameter pretrained model of Llama 2, a family of text-generation language models from Meta, converted for the Hugging Face Transformers format. It is the base model, not the dialogue-tuned Llama-2-Chat. The card says it takes text in and produces text out, was pretrained on 2 trillion tokens of publicly available online data, and has a 4k context length.
intended_use: Meta says Llama 2 is intended for commercial and research use in English, with tuned models meant for assistant-like chat and pretrained models adaptable to many text generation tasks. Out of scope are uses that break applicable laws, uses in languages other than English, and anything prohibited by the Acceptable Use Policy and licensing agreement.
what_sets_it_apart: Meta describes Llama 2 as an auto-regressive language model with an optimized transformer architecture. The 7B size does not use grouped-query attention, a way of sharing attention components to speed up inference, which the card lists only for the 70B size. The pretraining data has a cutoff of September 2022, and the card says the model was trained between January and July 2023.
running_it: The card says use of the model is governed by the Meta license, and that the license must be accepted on Meta's website before access is requested on Hugging Face.
limitations: Meta says testing was done in English and could not cover all scenarios. The model may produce inaccurate, biased or other objectionable responses, so developers should run safety testing and tuning for their own application before deploying it.
filters:
  size_category: small
  is_gated: true
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 174772
  likes: 2523
  last_modified: '2024-04-17'
  observed_at: '2026-11-20'
  source: https://huggingface.co/meta-llama/Llama-2-7b-hf
aliases:
- meta-llama/Llama-2-7b-hf
lineage:
  parents: []
  children: []
quotes:
  summary: This is the repository for the 7B pretrained model, converted for the Hugging Face Transformers format.
  intended_use: Llama 2 is intended for commercial and research use in English.
  what_sets_it_apart: Llama 2 is an auto-regressive language model that uses an optimized transformer architecture.
  running_it: Use of this model is governed by the Meta license.
  limitations: the model may in some instances produce inaccurate, biased or other objectionable responses to user prompts.
tags:
- llm
- text-generation
updated: '2026-11-23'
sources:
- url: https://huggingface.co/meta-llama/Llama-2-7b-hf
  title: meta-llama/Llama-2-7b-hf — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2307.09288
  title: arXiv:2307.09288
  trust: 0.95
---

## Summary

Llama 2 7B is the 7-billion-parameter pretrained model of Llama 2, a family of text-generation language models from Meta, converted for the Hugging Face Transformers format. It is the base model, not the dialogue-tuned Llama-2-Chat. The card says it takes text in and produces text out, was pretrained on 2 trillion tokens of publicly available online data, and has a 4k context length.

## Intended Use

Meta says Llama 2 is intended for commercial and research use in English, with tuned models meant for assistant-like chat and pretrained models adaptable to many text generation tasks. Out of scope are uses that break applicable laws, uses in languages other than English, and anything prohibited by the Acceptable Use Policy and licensing agreement.

## What Sets It Apart

Meta describes Llama 2 as an auto-regressive language model with an optimized transformer architecture. The 7B size does not use grouped-query attention, a way of sharing attention components to speed up inference, which the card lists only for the 70B size. The pretraining data has a cutoff of September 2022, and the card says the model was trained between January and July 2023.

## Running It

The card says use of the model is governed by the Meta license, and that the license must be accepted on Meta's website before access is requested on Hugging Face.

## Limitations

Meta says testing was done in English and could not cover all scenarios. The model may produce inaccurate, biased or other objectionable responses, so developers should run safety testing and tuning for their own application before deploying it.
