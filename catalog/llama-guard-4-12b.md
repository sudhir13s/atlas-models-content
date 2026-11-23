---
id: model/llama-guard-4-12b
type: model
slug: llama-guard-4-12b
schema_version: 1.1
title: Llama-Guard-4-12B
organization: organization/meta
release_date: '2025-04-23'
release_year: 2025
release_month: 4
license_note: llama4
model_category: language
tasks:
- task/safety-and-guard-models
- task/vision-language-models
total_parameters: 12001097216
summary: 'Llama Guard 4 is a safety classifier from Meta: it reads a prompt sent to a language model, or the model''s reply, and says whether it is safe or unsafe and, if unsafe, which content categories it violates. The card describes it as natively multimodal, with 12 billion parameters, trained jointly on text and multiple images, and pruned from the Llama 4 Scout pre-trained model.'
intended_use: The card says it can classify content in both language-model inputs (prompt classification) and language-model responses (response classification). It is trained to predict labels for the MLCommons hazard categories plus one added category, Code Interpreter Abuse, for text-only tool-call use.
what_sets_it_apart: The card says Llama Guard 4 combines the abilities of the earlier Llama Guard 3-8B and Llama Guard 3-11B-vision in a single classifier, handling English and multilingual text and mixed text-and-image prompts. Unlike Llama Guard 3-11B-vision, it can classify prompts that contain several images.
limitations: The card says its judgments, including common-sense knowledge, multilingual ability and policy coverage, may be limited by its training data. Categories such as Defamation, Intellectual Property and Elections may need up-to-date facts, performance was tested mostly with prompts of about three images, and as a language model it may be open to adversarial or prompt-injection attacks.
filters:
  size_category: medium
  is_gated: true
  pipeline_tag: image-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 189498
  likes: 141
  last_modified: '2025-04-29'
  observed_at: '2026-11-21'
  source: https://huggingface.co/meta-llama/Llama-Guard-4-12B
aliases:
- meta-llama/Llama-Guard-4-12B
lineage:
  parents: []
  children: []
quotes:
  summary: Llama Guard 4 is a natively multimodal safety classifier with 12 billion parameters trained jointly on text and multiple images.
  intended_use: Similar to previous versions, it can be used to classify content in both LLM inputs (prompt classification) and in LLM responses (response classification).
  what_sets_it_apart: Unlike Llama Guard 3-11B-vision, Llama Guard 4 now supports safety classification when multiple images are given in the prompt as input.
  limitations: Note that the performance of Llama Guard 4 was tested mostly with prompts containing a few images (three, most frequently), so performance may vary if using it to classify safety with a much larger number of images.
tags:
- llm
- image-text-to-text
updated: '2026-11-23'
sources:
- url: https://huggingface.co/meta-llama/Llama-Guard-4-12B
  title: meta-llama/Llama-Guard-4-12B — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2503.05731
  title: arXiv:2503.05731
  trust: 0.95
- url: https://arxiv.org/abs/2407.21783
  title: arXiv:2407.21783
  trust: 0.95
---

## Summary

Llama Guard 4 is a safety classifier from Meta: it reads a prompt sent to a language model, or the model's reply, and says whether it is safe or unsafe and, if unsafe, which content categories it violates. The card describes it as natively multimodal, with 12 billion parameters, trained jointly on text and multiple images, and pruned from the Llama 4 Scout pre-trained model.

## Intended Use

The card says it can classify content in both language-model inputs (prompt classification) and language-model responses (response classification). It is trained to predict labels for the MLCommons hazard categories plus one added category, Code Interpreter Abuse, for text-only tool-call use.

## What Sets It Apart

The card says Llama Guard 4 combines the abilities of the earlier Llama Guard 3-8B and Llama Guard 3-11B-vision in a single classifier, handling English and multilingual text and mixed text-and-image prompts. Unlike Llama Guard 3-11B-vision, it can classify prompts that contain several images.

## Limitations

The card says its judgments, including common-sense knowledge, multilingual ability and policy coverage, may be limited by its training data. Categories such as Defamation, Intellectual Property and Elections may need up-to-date facts, performance was tested mostly with prompts of about three images, and as a language model it may be open to adversarial or prompt-injection attacks.
