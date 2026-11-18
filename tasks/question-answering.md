---
id: task/question-answering
type: task
slug: question-answering
schema_version: 1.0
title: "Question Answering (Extractive)"
group: task-group/language
order: 9
summary: "Find the span of a given passage that answers a question."
hf_pipeline_tags: [question-answering]
inputs: [text]
outputs: [text]
tags: [question-answering, extractive, encoder]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/question-answering", title: "Hugging Face — Question Answering task", trust: 0.9 }
---

## What It Is
An extractive question answering model takes a question and a passage and **points to the answer inside the passage**.

- **Span selection:** the model predicts where the answer starts and ends; it writes no new text.
- **Traceable output:** every answer is a direct quotation with its position and a confidence score.
- **Passage required:** the model has no knowledge store of its own and reads only what it is given.
- **No-answer handling:** some models can report that the passage does not contain the answer.

## When To Use
- **Use it** to pull facts from contracts, manuals or search results when the answer must be an exact quotation.
- **Use text generation instead** when the answer must combine several passages or be phrased in new words.
- **Use token classification instead** when you want every entity of a type, not the answer to one question.
- **Use an embedding model first** when the right passage still has to be found among many documents.

## How To Choose
- **No-answer support:** whether the model can abstain instead of returning its best wrong span.
- **Maximum input length:** long passages are processed in overlapping windows, which slows each query.
- **Domain and language:** accuracy falls on text unlike the training passages.
- **Speed:** queries per second, which matters when many retrieved passages are checked per question.
