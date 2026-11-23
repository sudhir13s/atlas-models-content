---
id: task/code-models
type: task
slug: code-models
schema_version: 3.1
title: Code Models
group: task-group/language
parent: task/text-generation
order: 4
summary: Write, complete, explain and repair source code.
hf_pipeline_tags: []
hf_tags: []
inputs:
- text
outputs:
- text
benchmarks:
- benchmark/humaneval
- benchmark/livecodebench
- benchmark/swe-bench
tags:
- code
- llm
- generative
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/text-generation
  title: Hugging Face — Text Generation task
  trust: 0.9
---

## What It Is

A code model is a text generation model **trained mostly on source code**.

- **Completion:** continue the code at the cursor, including filling a gap between existing lines.
- **Instruction:** write a function, explain a file, or fix a failing test from a request.
- **Hugging Face has no separate tag**; these models are published under text generation.

## When To Use

- **Use it** for editor autocomplete, code review, test writing and repository-level changes.
- **Use a chat model instead** when code is only an occasional part of the conversation.
- **Use a tool-calling model as well** when the model must run commands and read their output.

## How To Choose

- **Fill-in-the-middle support** if the model will serve editor autocomplete.
- **Context window** large enough for the files it must read together.
- **Latency:** autocomplete needs a small, fast model; repository changes reward a larger one.
- **Benchmark type:** single-function tests show basic skill, repository-level tests show real work.
