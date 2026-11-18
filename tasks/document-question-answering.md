---
id: task/document-question-answering
type: task
slug: document-question-answering
schema_version: 1.0
title: "Document Question Answering"
group: task-group/documents
order: 2
summary: "Answer a text question from a form, invoice or scanned page by reading its text and layout."
hf_pipeline_tags: [document-question-answering]
inputs: [document, text]
outputs: [text]
tags: [documents, question-answering, extraction]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/document-question-answering", title: "Hugging Face — Document Question Answering task", trust: 0.9 }
---

## What It Is
A document question answering model takes a page image plus a question and **returns the answer found on the page**.

- **Layout-aware:** it uses where text sits on the page, so it can link a field name to its value.
- **Extractive models** copy the answer from the page; **generative models** write it out.
- **Typical targets:** totals, dates, names and reference numbers on forms, invoices and receipts.

## When To Use
- **Use it** to pull a few known values out of many documents with a similar purpose.
- **Use optical character recognition (OCR) instead** when you need the full text of the page.
- **Use table question answering instead** when the data is already a clean table rather than a page image.
- **Use a vision-language model instead** for open questions about photos or mixed visual content.

## How To Choose
- **Text reading:** some models need a separate text-recognition step first; others read the image directly.
- **Document types:** a model trained on forms may not handle dense reports or handwriting.
- **Page count:** most models read one page at a time, so long documents need a retrieval step.
- **Languages:** check coverage of the languages on your documents.
