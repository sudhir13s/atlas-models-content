---
id: task-group/retrieval
type: task-group
slug: retrieval
schema_version: 3.1
title: Retrieval
order: 2
summary: 'Models that turn content into vectors or relevance scores so the right item can be found: embeddings and rerankers.'
tags:
- retrieval
- embeddings
- search
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks
  title: Hugging Face — Tasks
  trust: 0.9
---

## Overview

Start here when the job is **finding the right item**, not writing an answer.

- **Embedding models** map text, images or pages to vectors; nearby vectors mean similar content.
- **Reranking models** score a query against each candidate and reorder a short list.
- **Typical pairing:** embeddings fetch a few hundred candidates fast, a reranker picks the best ten.
