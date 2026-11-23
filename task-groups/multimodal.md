---
id: task-group/multimodal
type: task-group
slug: multimodal
schema_version: 3.1
title: Multimodal
order: 7
summary: 'Single models that take and return several kinds of content at once: text, images, audio and video.'
tags:
- multimodal
- omni
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks
  title: Hugging Face — Tasks
  trust: 0.9
---

## Overview

Start here when **one model must handle several kinds of input and output** in the same conversation.

- **One model instead of a pipeline:** no separate speech recognizer, language model and voice.
- **The trade-off:** a model that does everything is usually weaker at each part than a specialist.
- **Two kinds only** (image plus text, or audio plus text) are covered in the vision and audio groups.
