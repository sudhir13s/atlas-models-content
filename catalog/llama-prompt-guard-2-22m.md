---
id: model/llama-prompt-guard-2-22m
type: model
slug: llama-prompt-guard-2-22m
schema_version: 1.1
title: Llama-Prompt-Guard-2-22M
organization: organization/meta
release_date: '2025-04-28'
release_year: 2025
release_month: 4
license_note: llama4
model_category: language
tasks:
- task/safety-and-guard-models
- task/text-classification
total_parameters: 70830722
summary: Llama Prompt Guard 2 22M is a small classifier from Meta that checks whether a piece of text is trying to subvert an AI application. It labels each prompt as benign or malicious, covering prompt injection, where untrusted data placed in the context makes a model follow unintended instructions, and jailbreaks, which are instructions meant to override a model's built-in safety features. The card says it has 22 million parameters, is based on DeBERTa-xsmall and supports a 512-token context window.
intended_use: The card says Prompt Guard is released as an open-source tool to help developers reduce prompt attack risks. A prompt is labeled malicious when it explicitly tries to override instructions given to a language model, judged only on that intent, whether or not the prompt is harmful or likely to succeed. For inputs longer than 512 tokens the card advises splitting them into segments and scanning them in parallel.
what_sets_it_apart: The card describes this as a smaller, faster version of the 86M model in the Prompt Guard 2 series, based on DeBERTa-xsmall, and states that it reduces latency and compute costs by 75% with minimal performance trade-offs. It lists simplified binary classification with no separate injection label, and a tokenization change meant to resist adversarial tricks such as whitespace manipulation and fragmented tokens. Both models were evaluated for attack detection in English, French, German, Hindi, Italian, Portuguese, Spanish and Thai.
filters:
  size_category: tiny
  is_gated: true
  pipeline_tag: text-classification
  library: transformers
hub_signals:
  downloads_30d: 6378
  likes: 94
  last_modified: '2025-04-29'
  observed_at: '2026-11-21'
  source: https://huggingface.co/meta-llama/Llama-Prompt-Guard-2-22M
aliases:
- meta-llama/Llama-Prompt-Guard-2-22M
lineage:
  parents: []
  children: []
quotes:
  summary: A smaller, faster version based on DeBERTa-xsmall.
  intended_use: to help developers reduce prompt attack risks with a straightforward yet highly customizable solution
  what_sets_it_apart: Both Prompt Guard 2 models focus on detecting explicit, known attack patterns
tags:
- llm
- text-classification
updated: '2026-11-23'
sources:
- url: https://huggingface.co/meta-llama/Llama-Prompt-Guard-2-22M
  title: meta-llama/Llama-Prompt-Guard-2-22M — model page (Hugging Face)
  trust: 0.9
---

## Summary

Llama Prompt Guard 2 22M is a small classifier from Meta that checks whether a piece of text is trying to subvert an AI application. It labels each prompt as benign or malicious, covering prompt injection, where untrusted data placed in the context makes a model follow unintended instructions, and jailbreaks, which are instructions meant to override a model's built-in safety features. The card says it has 22 million parameters, is based on DeBERTa-xsmall and supports a 512-token context window.

## Intended Use

The card says Prompt Guard is released as an open-source tool to help developers reduce prompt attack risks. A prompt is labeled malicious when it explicitly tries to override instructions given to a language model, judged only on that intent, whether or not the prompt is harmful or likely to succeed. For inputs longer than 512 tokens the card advises splitting them into segments and scanning them in parallel.

## What Sets It Apart

The card describes this as a smaller, faster version of the 86M model in the Prompt Guard 2 series, based on DeBERTa-xsmall, and states that it reduces latency and compute costs by 75% with minimal performance trade-offs. It lists simplified binary classification with no separate injection label, and a tokenization change meant to resist adversarial tricks such as whitespace manipulation and fragmented tokens. Both models were evaluated for attack detection in English, French, German, Hindi, Italian, Portuguese, Spanish and Thai.
