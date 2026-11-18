---
id: task/tool-calling-models
type: task
slug: tool-calling-models
schema_version: 1.0
title: "Tool-Calling Models"
group: task-group/language
parent: task/text-generation
order: 5
summary: "Emit structured function calls so a program can run tools and drive an agent loop."
hf_pipeline_tags: []
inputs: [text]
outputs: [text]
benchmarks: [benchmark/bfcl-v4, benchmark/tau2-bench]
tags: [tool-calling, agents, llm]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/text-generation", title: "Hugging Face — Text Generation task", trust: 0.9 }
---

## What It Is
A tool-calling model is a text generation model **trained to request actions in a machine-readable format**.

- **Tool list in:** the prompt describes each available function, its purpose and its arguments.
- **Structured call out:** the model replies with a function name and arguments, usually as JSON.
- **Agent loop:** the calling program runs the function, returns the result, and the model decides the next step.
- **Hugging Face has no separate tag**; these models are published under text generation.

## When To Use
- **Use it** when the model must search, query a database, call an API or edit files to finish a job.
- **Use a chat model instead** when every answer can be written from the prompt alone.
- **Use a reasoning model as well** when choosing the right sequence of actions is the hard part.
- **Use a code model as well** when the tools are a shell, an editor and a test runner.

## How To Choose
- **Call accuracy:** how often the model picks the right function and fills valid arguments.
- **Schema adherence:** whether output always parses, including nested and optional arguments.
- **Parallel and multi-step calls:** support for several calls in one turn and for long chains of turns.
- **Restraint:** whether the model declines to call a tool when none fits or information is missing.
