---
id: model/phi-2
type: model
slug: phi-2
schema_version: 1.1
title: phi-2
organization: organization/microsoft
release_date: '2023-12-13'
release_year: 2023
release_month: 12
license: license/mit
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 2.78
total_parameters: 2779683840
context_window_tokens: 2048
summary: Phi-2 is a small language model from Microsoft with 2.7 billion parameters. According to the model card it was trained on the same data sources as Phi-1.5, plus a new source of synthetic NLP texts and filtered websites. It has not been fine-tuned with reinforcement learning from human feedback, so it is a base model rather than a chat assistant.
intended_use: The card says Phi-2 is best suited to prompts in the question-and-answer, chat and code formats, and that the open-source release is meant to give the research community a small, unrestricted model for exploring safety challenges such as toxicity and societal bias. It puts direct adoption for production tasks without evaluation out of scope and says outputs should be treated as a starting point.
what_sets_it_apart: The card says the new training data is made of synthetic NLP texts and websites filtered for safety and educational value. It also says the model has not undergone instruction fine-tuning or reinforcement learning from human feedback, and that it tends to give textbook-like answers because its training data is mainly textbooks.
running_it: The card says Phi-2 is integrated in Transformers version 4.37.0 or later, and that older versions need trust_remote_code=True. It warns of an attention overflow issue with FP16, which can be handled by enabling or disabling autocast in the PhiAttention.forward() function.
fingerprint:
  attention_type: MHA
  num_layers: 32
  attention_heads: 32
  kv_heads: 32
  head_size: 80
  hidden_size: 2560
  intermediate_size: 10240
  context_window_tokens: 2048
  vocabulary_size: 51200
  activation_function: gelu_new
  normalization: LayerNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 185045
  likes: 3538
  last_modified: '2025-12-08'
  observed_at: '2026-11-20'
  source: https://huggingface.co/microsoft/phi-2
aliases:
- microsoft/phi-2
lineage:
  parents: []
  children: []
quotes:
  summary: Our model hasn't been fine-tuned through reinforcement learning from human feedback.
  intended_use: Direct adoption for production tasks without evaluation is out of scope of this project.
  what_sets_it_apart: augmented with a new data source that consists of various NLP synthetic texts and filtered websites (for safety and educational value)
  running_it: Phi-2 is known for having an attention overflow issue (with FP16).
tags:
- llm
- text-generation
- phi
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/microsoft/phi-2
  title: microsoft/phi-2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/phi-2/blob/main/config.json
  title: microsoft/phi-2 — config.json
  trust: 0.9
---

## Summary

Phi-2 is a small language model from Microsoft with 2.7 billion parameters. According to the model card it was trained on the same data sources as Phi-1.5, plus a new source of synthetic NLP texts and filtered websites. It has not been fine-tuned with reinforcement learning from human feedback, so it is a base model rather than a chat assistant.

## Intended Use

The card says Phi-2 is best suited to prompts in the question-and-answer, chat and code formats, and that the open-source release is meant to give the research community a small, unrestricted model for exploring safety challenges such as toxicity and societal bias. It puts direct adoption for production tasks without evaluation out of scope and says outputs should be treated as a starting point.

## What Sets It Apart

The card says the new training data is made of synthetic NLP texts and websites filtered for safety and educational value. It also says the model has not undergone instruction fine-tuning or reinforcement learning from human feedback, and that it tends to give textbook-like answers because its training data is mainly textbooks.

## Running It

The card says Phi-2 is integrated in Transformers version 4.37.0 or later, and that older versions need trust_remote_code=True. It warns of an attention overflow issue with FP16, which can be handled by enabling or disabling autocast in the PhiAttention.forward() function.
