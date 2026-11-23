---
id: task-group/documents
type: task-group
slug: documents
schema_version: 3.1
title: Documents
order: 4
summary: 'Models that read scanned pages, forms and tables: text recognition, document questions and table questions.'
tags:
- documents
- ocr
- tables
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks
  title: Hugging Face — Tasks
  trust: 0.9
---

## Overview

Start here when the input is a **page, a form, a receipt or a table** rather than a photograph.

- **Reading:** optical character recognition (OCR) turns a page image into text or Markdown.
- **Asking:** document and table question answering return a specific value from the page.
- **Layout matters here**; a general vision model often loses columns, tables and reading order.
