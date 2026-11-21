---
id: task/ocr
type: task
slug: ocr
schema_version: 1.0
title: "Optical Character Recognition (OCR)"
group: task-group/documents
order: 1
summary: "Convert a page image or scanned document into text or Markdown, including tables and formulas."
hf_pipeline_tags: []
hf_tags: [ocr]
inputs: [document, image]
outputs: [text]
tags: [ocr, documents, text-extraction]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/image-to-text", title: "Hugging Face — Image-to-Text task", trust: 0.9 }
---

## What It Is
Optical character recognition (OCR) **transcribes the text shown in an image** into machine-readable text.

- **Plain transcription** returns the characters on the page in reading order.
- **Structured output** keeps headings, tables and formulas, often as Markdown or a similar markup.
- **Hugging Face has no separate tag**; these models are published under image-to-text and image-text-to-text.

## When To Use
- **Use it** to make scans, photos of pages and image-only files searchable or ready for a language model.
- **Use document question answering instead** when you need one value from a page, not the whole text.
- **Use image and multimodal embeddings instead** when you only need to find the right page, not read it.
- **Use a vision-language model instead** for open questions about photos, charts or screenshots.

## How To Choose
- **Layout handling:** multi-column pages, tables and formulas are where models differ most.
- **Languages and scripts:** check coverage, including handwriting if you need it.
- **Output format:** plain text, Markdown, or text with positions on the page.
- **Speed per page:** small specialised models are far quicker than general vision-language models.
