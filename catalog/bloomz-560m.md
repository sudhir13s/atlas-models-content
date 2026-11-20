---
id: model/bloomz-560m
type: model
slug: bloomz-560m
schema_version: 1.1
title: bloomz-560m
organization: organization/bigscience
release_date: '2022-10-08'
release_year: 2022
release_month: 10
license: license/bigscience-bloom-rail-1-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 0.56
total_parameters: 559214592
context_window_tokens: 2048
summary: bloomz-560m is a multitask finetuned model from BigScience created by finetuning bloom-560m pretrained multilingual language models on the xP3 crosslingual task mixture. Released on 2022-10-08 with 0.559 billion parameters and a 2048 token context window under the BigScience RAIL License v1.0, it is part of a family of models capable of following human instructions in dozens of languages zero-shot.
why_it_matters: The resulting models are capable of crosslingual generalization to unseen tasks and languages.
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  context_window_tokens: 2048
  vocabulary_size: 250880
  normalization: LayerNorm
  positional_encoding: ALiBi
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 767239
  likes: 149
  last_modified: '2023-05-27'
  observed_at: '2026-11-19'
  source: https://huggingface.co/bigscience/bloomz-560m
aliases:
- bigscience/bloomz-560m
lineage:
  parents: []
  children: []
quote: We finetune BLOOM & mT5 pretrained multilingual language models on our crosslingual task mixture (xP3) and find the resulting models capable of crosslingual generalization to unseen tasks & languages.
review:
  score: 10.0
  reviewed_by:
    provider: nvidia-gemma-4-31b
    model: nvidia_nim/google/gemma-4-31b-it
  prompt_versions:
    writer: 1.0.0
    critic: 1.2.0
generated_by:
  job: backfill
  run_id: 6
  provider: gemini-flash-lite
  model: gemini/gemini-3.5-flash-lite
  prompt_version: 1.0.0
tags:
- llm
- text-generation
- bloom
- dense
- mha
updated: '2026-11-20'
sources:
- url: https://huggingface.co/bigscience/bloomz-560m
  title: bigscience/bloomz-560m — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/bigscience/bloomz-560m/blob/main/config.json
  title: bigscience/bloomz-560m — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2211.01786
  title: arXiv:2211.01786
  trust: 0.95
---

## Summary

bloomz-560m is a multitask finetuned model from BigScience created by finetuning bloom-560m pretrained multilingual language models on the xP3 crosslingual task mixture. Released on 2022-10-08 with 0.559 billion parameters and a 2048 token context window under the BigScience RAIL License v1.0, it is part of a family of models capable of following human instructions in dozens of languages zero-shot.

## Why It Matters

The resulting models are capable of crosslingual generalization to unseen tasks and languages.
