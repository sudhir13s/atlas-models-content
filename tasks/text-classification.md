---
id: task/text-classification
type: task
slug: text-classification
schema_version: 1.0
title: "Text Classification"
group: task-group/language
order: 6
summary: "Assign one or more labels from a fixed set to a whole piece of text."
hf_pipeline_tags: [text-classification]
inputs: [text]
outputs: [label]
tags: [classification, encoder, text]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/text-classification", title: "Hugging Face — Text Classification task", trust: 0.9 }
---

## What It Is
A text classification model reads a passage and **returns a label with a confidence score**.

- **Fixed label set:** the possible labels are decided when the model is trained.
- **Common jobs:** sentiment, topic, intent, language detection and toxicity.
- **Single or multi-label:** some models pick exactly one label, others may return several.
- **Small and fast:** most are compact encoder models that run well without a graphics card.

## When To Use
- **Use it** when the same set of labels is applied to large volumes of text.
- **Use zero-shot classification instead** when the labels change often or no training data exists.
- **Use token classification instead** when the label belongs to individual words, not the whole passage.
- **Use text generation instead** when the output must be an explanation, not a category.

## How To Choose
- **Label match:** whether the model's labels are the ones you need, or you must fine-tune.
- **Domain and language:** a model trained on reviews may misjudge legal, medical or chat text.
- **Maximum input length:** longer documents are cut off unless split into pieces first.
- **Throughput:** passages per second on your hardware, which a smaller model improves.
