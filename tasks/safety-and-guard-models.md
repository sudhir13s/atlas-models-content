---
id: task/safety-and-guard-models
type: task
slug: safety-and-guard-models
schema_version: 3.1
title: Safety and Guard Models
group: task-group/language
parent: task/text-classification
order: 7
summary: Screen prompts and responses for unsafe content, prompt injection and personal data.
hf_pipeline_tags: []
hf_tags:
- safety
- guardrail
inputs:
- text
outputs:
- label
tags:
- safety
- guardrails
- moderation
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/text-classification
  title: Hugging Face — Text Classification task
  trust: 0.9
---

## What It Is

A guard model is a classifier that **checks text going into or coming out of another model**.

- **Input screening:** flags harmful requests and prompt injection, where hidden text tries to override instructions.
- **Output screening:** flags unsafe replies before they reach the reader.
- **Personal data detection:** marks names, contact details and similar information for removal.
- **Hugging Face has no separate tag**; these models are published under text classification or text generation.

## When To Use

- **Use it** in front of and behind any assistant that handles text from people or sources you do not control.
- **Use text classification instead** for a single narrow check, such as toxicity in comments.
- **Use token classification instead** when you must locate and mask each personal detail, not only detect it.
- **Do not rely on it alone:** pair it with access controls and limits on what the main model may do.

## How To Choose

- **Policy coverage:** which harm categories the model knows, and whether you can supply your own.
- **Error balance:** missed harmful text against harmless text wrongly blocked, on examples like yours.
- **Added latency:** the check runs on every request, so size and speed matter.
- **Language coverage:** detection is often weaker outside the main training languages.
