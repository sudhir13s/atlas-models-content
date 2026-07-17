---
id: architecture-era/gpt
type: architecture-era
slug: gpt
schema_version: 1.0
title: "GPT (Decoder-Only Autoregressive LMs)"
name: "GPT"
era: scaling
era_range: "2018–2020"
year: 2020
family: llm
core_innovation: "Decoder-only autoregressive pretraining that, scaled up, yields in-context (few-shot) learning without task-specific fine-tuning."
description: "The decoder-only generative line whose scaling produced emergent few-shot, in-context learning."
key_features: ["Decoder-only transformer", "Causal (left-to-right) language modeling", "Massive scale", "In-context / few-shot learning", "Task-agnostic prompting"]
key_models: ["GPT (2018)", "GPT-2 (2019)", "GPT-3 175B (2020)"]
paper_title: "Language Models are Few-Shot Learners (GPT-3)"
paper_url: "https://arxiv.org/abs/2005.14165"
influenced_by: [architecture-era/transformer]
led_to: [architecture-era/llama-recipe, architecture-era/scaling-laws, architecture-era/rlhf]
tags: [gpt, decoder, autoregressive, in-context-learning, llm]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2005.14165", title: "Language Models are Few-Shot Learners (Brown et al., 2020)", trust: 0.95 }
  - { url: "https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf", title: "Language Models are Unsupervised Multitask Learners (GPT-2, Radford et al., 2019)", trust: 0.9 }
---

## What Changed
Where BERT used the transformer encoder for understanding, the GPT line used the **decoder** for
generation, trained on a single, brutally simple objective: predict the next token given all previous
ones. The surprise was what scale did to this objective. GPT (2018) showed a pretrained decoder could
be fine-tuned across tasks; GPT-2 (2019) showed it could do many tasks zero-shot; and GPT-3 (2020),
at 175B parameters, showed **in-context learning** — you describe a task and give a few examples *in
the prompt*, and the frozen model performs it with no weight updates at all. This collapsed the
"pretrain then fine-tune per task" workflow into "pretrain once, then prompt." Next-token prediction
turned out to be a general-purpose training signal, and the practical interface to AI became natural
language. GPT descends from the transformer and directly motivated the scaling-laws work that governs
how big to train, the open LLaMA recipe, and the RLHF alignment step that made these models usable as
assistants.

## Key Ideas
- **Decoder-only, causal** — a triangular attention mask lets each token see only the past, so the
  same network trains by prediction and generates by sampling.
- **Scale as a lever** — capability rose smoothly and sometimes discontinuously with parameters, data,
  and compute; bigger models did qualitatively new things.
- **In-context learning** — few-shot examples in the prompt steer a frozen model, trading fine-tuning
  for prompting and making one model serve countless tasks.
- **Prompting as interface** — the task specification moves from labelled datasets to natural-language
  instructions, reshaping how people build on top of models.
