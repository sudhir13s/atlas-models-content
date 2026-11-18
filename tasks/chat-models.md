---
id: task/chat-models
type: task
slug: chat-models
schema_version: 1.0
title: "Chat Models"
group: task-group/language
parent: task/text-generation
order: 2
summary: "Hold a multi-turn conversation and act as a general-purpose assistant."
hf_pipeline_tags: []
inputs: [text]
outputs: [text]
benchmarks: [benchmark/mmlu-pro, benchmark/ifeval]
tags: [chat, assistant, llm]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/text-generation", title: "Hugging Face — Text Generation task", trust: 0.9 }
---

## What It Is
A chat model is a text generation model **tuned to follow instructions across a conversation**.

- **Turn structure:** the input is a list of system, user and assistant messages, not one raw passage.
- **Chat template:** each model expects its own formatting of those messages, applied before generation.
- **General purpose:** one model drafts, explains, rewrites and answers questions without task-specific training.
- **Hugging Face has no separate tag**; these models are published under text generation.

## When To Use
- **Use it** for assistants, support bots, drafting and question answering over text you supply in the prompt.
- **Use a reasoning model instead** when the problem needs long multi-step working, such as proofs or planning.
- **Use a code model instead** when most requests are about source code.
- **Use a tool-calling model instead** when the model must act on other systems, not only reply.

## How To Choose
- **Instruction following:** how reliably the model keeps to format, length and system-prompt rules.
- **Context window:** how much conversation history and pasted material fits in one request.
- **Size against hardware:** parameter count and quantization decide memory use and response speed.
- **Language coverage:** quality often drops outside the languages that dominate the training data.
