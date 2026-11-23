---
id: task/summarization
type: task
slug: summarization
schema_version: 3.1
title: Summarization
group: task-group/language
order: 12
summary: Shorten a document while keeping its main points.
hf_pipeline_tags:
- summarization
inputs:
- text
outputs:
- text
tags:
- summarization
- sequence-to-sequence
- text
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/summarization
  title: Hugging Face — Summarization task
  trust: 0.9
---

## What It Is

A summarization model reads a long text and **writes a shorter version of it**.

- **Abstractive:** the model writes new sentences that restate the source.
- **Extractive:** the model selects the most important existing sentences and returns them unchanged.
- **Dedicated models:** most are compact encoder-decoder models trained on document and summary pairs.
- **Fixed style:** a dedicated model produces the length and tone of its training data, such as news briefs.

## When To Use

- **Use it** for high-volume, uniform jobs such as condensing articles, tickets or transcripts.
- **Use a chat model instead** when each request needs a different length, focus, format or audience.
- **Use extractive question answering instead** when you want one specific fact, not an overview.
- **Use text classification instead** when a topic label is enough.

## How To Choose

- **Maximum input length:** whether the whole document fits, or it must be summarized in parts.
- **Faithfulness:** how often the summary states something the source does not support.
- **Training domain:** news, dialogue, scientific and legal text each call for a matching model.
- **Length control:** whether the output length can be set and is respected.
