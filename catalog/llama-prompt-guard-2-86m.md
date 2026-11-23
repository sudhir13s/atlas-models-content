---
id: model/llama-prompt-guard-2-86m
type: model
slug: llama-prompt-guard-2-86m
schema_version: 1.1
title: Llama-Prompt-Guard-2-86M
organization: organization/meta
release_date: '2025-04-28'
release_year: 2025
release_month: 4
license_note: llama4
model_category: language
tasks:
- task/safety-and-guard-models
- task/text-classification
total_parameters: 278810882
summary: Llama Prompt Guard 2 86M is a text classifier from Meta that checks the text sent to a language model and labels it benign or malicious. It is built to catch prompt injections, where untrusted data tries to make a model follow unintended instructions, and jailbreaks, where instructions try to override a model's built-in safety features. The card says it is trained on a large corpus of known vulnerabilities and released as an open-source tool for developers.
intended_use: The card presents it as a tool that helps developers reduce prompt attack risks in applications built on language models. It says the model labels a prompt malicious only if the prompt explicitly tries to override instructions given to or seen by a language model, whether or not it is harmful or likely to succeed.
what_sets_it_apart: Compared with Prompt Guard 1, the card lists a simplified two-way label (benign or malicious) with no separate injection label, tokenization refined to resist tricks such as whitespace manipulation and fragmented tokens, and a multilingual base model so it can detect English and non-English attacks. It reports evaluation in English, French, German, Hindi, Italian, Portuguese, Spanish and Thai.
running_it: The card says both Prompt Guard 2 models support a 512-token context window. For longer inputs it advises splitting the prompt into segments and scanning them in parallel so that violations are not missed.
filters:
  size_category: tiny
  is_gated: true
  pipeline_tag: text-classification
  library: transformers
hub_signals:
  downloads_30d: 99107
  likes: 402
  last_modified: '2025-04-29'
  observed_at: '2026-11-21'
  source: https://huggingface.co/meta-llama/Llama-Prompt-Guard-2-86M
aliases:
- meta-llama/Llama-Prompt-Guard-2-86M
lineage:
  parents: []
  children: []
quotes:
  summary: Both Llama Prompt Guard 2 models detect both prompt injection and jailbreaking attacks, trained on a large corpus of known vulnerabilities.
  intended_use: We’re releasing Prompt Guard as an open-source tool to help developers reduce prompt attack risks with a straightforward yet highly customizable solution.
  what_sets_it_apart: We refined the tokenization strategy to mitigate adversarial tokenization attacks, such as whitespace manipulations and fragmented tokens.
  running_it: For longer inputs, split prompts into segments and scan them in parallel to ensure violations are detected.
tags:
- llm
- text-classification
updated: '2026-11-23'
sources:
- url: https://huggingface.co/meta-llama/Llama-Prompt-Guard-2-86M
  title: meta-llama/Llama-Prompt-Guard-2-86M — model page (Hugging Face)
  trust: 0.9
---

## Summary

Llama Prompt Guard 2 86M is a text classifier from Meta that checks the text sent to a language model and labels it benign or malicious. It is built to catch prompt injections, where untrusted data tries to make a model follow unintended instructions, and jailbreaks, where instructions try to override a model's built-in safety features. The card says it is trained on a large corpus of known vulnerabilities and released as an open-source tool for developers.

## Intended Use

The card presents it as a tool that helps developers reduce prompt attack risks in applications built on language models. It says the model labels a prompt malicious only if the prompt explicitly tries to override instructions given to or seen by a language model, whether or not it is harmful or likely to succeed.

## What Sets It Apart

Compared with Prompt Guard 1, the card lists a simplified two-way label (benign or malicious) with no separate injection label, tokenization refined to resist tricks such as whitespace manipulation and fragmented tokens, and a multilingual base model so it can detect English and non-English attacks. It reports evaluation in English, French, German, Hindi, Italian, Portuguese, Spanish and Thai.

## Running It

The card says both Prompt Guard 2 models support a 512-token context window. For longer inputs it advises splitting the prompt into segments and scanning them in parallel so that violations are not missed.
