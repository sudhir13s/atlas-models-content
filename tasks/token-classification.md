---
id: task/token-classification
type: task
slug: token-classification
schema_version: 1.0
title: "Token Classification"
group: task-group/language
order: 8
summary: "Label each word or token in a text, for example to mark names, places and parts of speech."
hf_pipeline_tags: [token-classification]
inputs: [text]
outputs: [label]
tags: [ner, tagging, encoder]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/token-classification", title: "Hugging Face — Token Classification task", trust: 0.9 }
---

## What It Is
A token classification model **gives a label to every token** instead of one label for the whole text.

- **Named entity recognition (NER):** marks spans such as people, organisations, places and dates.
- **Part-of-speech (POS) tagging:** marks each word as a noun, verb, adjective and so on.
- **Span output:** adjacent tokens with the same label are merged into one span with start and end positions.
- **Fixed label set:** the entity or tag types are decided when the model is trained.

## When To Use
- **Use it** to pull structured fields out of text or to find and mask personal details.
- **Use text classification instead** when one label for the whole passage is enough.
- **Use extractive question answering instead** when the span you want is described by a question, not a fixed type.
- **Use text generation instead** when the entity types change per request or the output needs nested structure.

## How To Choose
- **Entity types:** whether the model's label set covers the fields you need.
- **Domain and language:** general-news models miss clinical, legal or product terms.
- **Tagging scheme and alignment:** how sub-word pieces map back to words and character offsets.
- **Maximum input length:** long documents must be split, which can cut an entity in two.
