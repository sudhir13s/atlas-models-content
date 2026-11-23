---
id: task/text-generation
type: task
slug: text-generation
schema_version: 3.1
title: Text Generation
group: task-group/language
order: 1
summary: Continue or answer a text prompt with new text; the task behind every large language model.
hf_pipeline_tags:
- text-generation
inputs:
- text
outputs:
- text
tags:
- llm
- generative
- text
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/text-generation
  title: Hugging Face — Text Generation task
  trust: 0.9
---

## What It Is

A text generation model takes text in and **writes the text that should follow**.

- **Instruction-tuned ("instruct" or "chat") models** answer a request.
- **Base models** only continue a passage and are used mainly as a starting point for fine-tuning.
- **Narrower jobs** sit under this task: chat, reasoning, code and tool calling.

## When To Use

- **Use it** when the output is free-form text and the request changes from call to call.
- **Use a narrower task** below when the work is mostly code, multi-step reasoning or calling tools.
- **Use text classification or an embedding model instead** for a fixed label or a similarity search; they are far smaller and cheaper.

## How To Choose

- **Size against hardware:** parameter count and quantization decide whether the model fits in memory.
- **Context window:** the longest prompt plus answer the model can hold.
- **Licence:** open weights do not always allow commercial use.
- **Instruct or base:** pick an instruct model unless you plan to fine-tune.
