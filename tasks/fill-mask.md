---
id: task/fill-mask
type: task
slug: fill-mask
schema_version: 3.1
title: Fill-Mask
group: task-group/language
order: 11
summary: Predict a hidden word in a sentence; the training task of encoder models used as a base for fine-tuning.
hf_pipeline_tags:
- fill-mask
inputs:
- text
outputs:
- text
tags:
- masked-language-model
- encoder
- pretraining
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/fill-mask
  title: Hugging Face — Fill-Mask task
  trust: 0.9
---

## What It Is

A fill-mask model, also called a masked language model, **predicts the word hidden behind a mask token**.

- **Two-sided context:** the model reads the words on both sides of the gap at once.
- **Encoder-only design:** it builds a representation of the text and does not write long passages.
- **Ranked candidates:** the output is a short list of likely words with a score for each.
- **Mostly a starting point:** these models are usually fine-tuned for another task, not used directly.

## When To Use

- **Use it** as the base model to fine-tune for classification, tagging, extractive question answering or retrieval.
- **Use it directly** only to probe what a model has learned or to suggest a single missing word.
- **Use text generation instead** when you need new sentences or paragraphs.
- **Use text classification or token classification instead** when a ready fine-tuned model already fits the job.

## How To Choose

- **Training domain and language:** a base trained on text like yours fine-tunes better.
- **Maximum input length:** older encoders accept only short passages; newer ones accept far longer ones.
- **Size:** smaller or distilled encoders trade a little accuracy for speed and memory.
- **Licence:** it carries over to every model you fine-tune from the base.
