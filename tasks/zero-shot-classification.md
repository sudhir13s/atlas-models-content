---
id: task/zero-shot-classification
type: task
slug: zero-shot-classification
schema_version: 1.0
title: "Zero-Shot Classification"
group: task-group/language
order: 10
summary: "Classify text into labels supplied at request time, with no training on those labels."
hf_pipeline_tags: [zero-shot-classification]
inputs: [text]
outputs: [label]
tags: [classification, zero-shot, text]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/zero-shot-classification", title: "Hugging Face — Zero-Shot Classification task", trust: 0.9 }
---

## What It Is
A zero-shot classification model **scores text against label names you provide with each request**.

- **Labels as text:** the candidate labels are plain words or phrases, so they can change at any time.
- **Entailment method:** many models test whether the text supports a sentence such as "this is about sport".
- **Scores per label:** the result ranks every candidate, and can allow several labels to apply.
- **No training step:** nothing is fine-tuned, so a new category works immediately.

## When To Use
- **Use it** to prototype a classifier, route varied requests, or label data before any examples exist.
- **Use text classification instead** once labels are stable and examples exist; a trained model is faster and more accurate.
- **Use text generation instead** when categories need long definitions or a written justification.
- **Use an embedding model instead** to group text by similarity when no label names are known.

## How To Choose
- **Cost per label:** entailment models run once per candidate, so many labels multiply the time.
- **Label wording sensitivity:** how much the scores shift when a label is rephrased.
- **Multi-label support:** whether labels are scored independently or forced to compete.
- **Language coverage:** whether both the text and the label names can be in your language.
