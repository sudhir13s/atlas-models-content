---
id: task/table-question-answering
type: task
slug: table-question-answering
schema_version: 3.1
title: Table Question Answering
group: task-group/documents
order: 3
summary: Answer a text question using the cells of a table.
hf_pipeline_tags:
- table-question-answering
inputs:
- tabular
- text
outputs:
- text
tags:
- tables
- question-answering
- structured-data
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/table-question-answering
  title: Hugging Face — Table Question Answering task
  trust: 0.9
---

## What It Is

A table question answering model reads a table and a question and **returns the answer from the table's cells**.

- **Cell selection:** the model picks the cells that hold the answer.
- **Aggregation:** some models also sum, count or average the selected cells.
- **Structured input:** the table arrives as rows and columns, not as an image.

## When To Use

- **Use it** for quick lookups over small tables without writing a query.
- **Use document question answering instead** when the table is inside a scanned page or image.
- **Use optical character recognition (OCR) first** to turn a pictured table into rows and columns.
- **Use text generation instead** to write a database query when the table is too large to pass to a model.

## How To Choose

- **Table size limit:** rows and columns beyond the input length are dropped.
- **Aggregation support:** not every model can count, sum or compare across cells.
- **Answer form:** selected cells only, or a written sentence.
- **Languages and number formats:** check handling of the headers, dates and units in your tables.
