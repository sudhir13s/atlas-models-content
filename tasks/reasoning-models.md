---
id: task/reasoning-models
type: task
slug: reasoning-models
schema_version: 1.0
title: "Reasoning Models"
group: task-group/language
parent: task/text-generation
order: 3
summary: "Work through a problem step by step before giving a final answer."
hf_pipeline_tags: []
inputs: [text]
outputs: [text]
benchmarks: [benchmark/gpqa-diamond, benchmark/aime, benchmark/math]
tags: [reasoning, llm, generative]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/text-generation", title: "Hugging Face — Text Generation task", trust: 0.9 }
---

## What It Is
A reasoning model is a text generation model **trained to write out intermediate working before it answers**.

- **Thinking phase:** the model first produces a scratchpad of steps, checks and corrections.
- **Final answer:** a shorter reply follows, built on that working.
- **Extra cost:** the thinking text is generated like any other output, so it adds time and tokens.
- **Hugging Face has no separate tag**; these models are published under text generation.

## When To Use
- **Use it** for mathematics, science questions, logic puzzles and plans with many dependent steps.
- **Use a chat model instead** for everyday conversation, drafting and lookups, where the thinking adds only delay.
- **Use a code model instead** when the hard part is writing or repairing software.
- **Use a tool-calling model as well** when the plan must be carried out, not only written down.

## How To Choose
- **Thinking control:** whether the amount of reasoning can be capped, lowered or switched off per request.
- **Visible working:** whether the reasoning text is returned, which matters for auditing an answer.
- **Latency and cost:** total tokens per answer, including the thinking, at the depth you need.
- **Context window:** long working uses the same budget as the prompt and the answer.
