---
id: model/pythia-160m
type: model
slug: pythia-160m
schema_version: 1.1
title: pythia-160m
organization: organization/eleutherai
release_date: '2023-02-08'
release_year: 2023
release_month: 2
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 0.21
total_parameters: 212654688
context_window_tokens: 2048
summary: Pythia-160M is a 160-million-parameter English language model from EleutherAI, part of the Pythia Scaling Suite, a collection built to support research on large language models, especially interpretability research (understanding how they work inside). This version was trained on the Pile, a large public text dataset.
intended_use: EleutherAI says the primary intended use is research on the behavior, functionality and limitations of large language models, in a controlled setting. Out-of-scope uses are deployment as a product or in human-facing interactions, translation or text in languages other than English, and use as a chatbot like ChatGPT, since it was not fine-tuned to follow instructions.
what_sets_it_apart: The card says all eight Pythia sizes were trained on the exact same data in the exact same order. Each size has two models, one trained on the Pile and one trained on the Pile after global deduplication, and each model has 154 intermediate checkpoints hosted as branches on Hugging Face.
limitations: The card says never to rely on the model to produce factually accurate output. The Pile is known to contain profanity and offensive text, so the model may produce socially unacceptable text even when the prompt is not offensive, and the card advises a human to curate any outputs shown to others.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 2048
  vocabulary_size: 50304
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: pre
  stack: decoder
  parallel_sublayers: separate-norms
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 2815994
  likes: 67
  last_modified: '2023-07-09'
  observed_at: '2026-11-20'
  source: https://huggingface.co/EleutherAI/pythia-160m
aliases:
- EleutherAI/pythia-160m
lineage:
  parents: []
  children: []
quotes:
  summary: The Pythia model suite was deliberately designed to promote scientific research on large language models, especially interpretability research.
  intended_use: The primary intended use of Pythia is research on the behavior, functionality, and limitations of large language models.
  what_sets_it_apart: All 8 model sizes are trained on the exact same data, in the exact same order.
  limitations: Never rely on Pythia-160M to produce factually accurate output.
tags:
- llm
- text-generation
- gpt-neox
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/EleutherAI/pythia-160m
  title: EleutherAI/pythia-160m — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/EleutherAI/pythia-160m/blob/main/config.json
  title: EleutherAI/pythia-160m — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2304.01373
  title: arXiv:2304.01373
  trust: 0.95
- url: https://arxiv.org/abs/2101.00027
  title: arXiv:2101.00027
  trust: 0.95
- url: https://arxiv.org/abs/2201.07311
  title: arXiv:2201.07311
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gpt_neox/modeling_gpt_neox.py
  title: 'transformers/models/gpt_neox/modeling_gpt_neox.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Pythia-160M is a 160-million-parameter English language model from EleutherAI, part of the Pythia Scaling Suite, a collection built to support research on large language models, especially interpretability research (understanding how they work inside). This version was trained on the Pile, a large public text dataset.

## Intended Use

EleutherAI says the primary intended use is research on the behavior, functionality and limitations of large language models, in a controlled setting. Out-of-scope uses are deployment as a product or in human-facing interactions, translation or text in languages other than English, and use as a chatbot like ChatGPT, since it was not fine-tuned to follow instructions.

## What Sets It Apart

The card says all eight Pythia sizes were trained on the exact same data in the exact same order. Each size has two models, one trained on the Pile and one trained on the Pile after global deduplication, and each model has 154 intermediate checkpoints hosted as branches on Hugging Face.

## Limitations

The card says never to rely on the model to produce factually accurate output. The Pile is known to contain profanity and offensive text, so the model may produce socially unacceptable text even when the prompt is not offensive, and the card advises a human to curate any outputs shown to others.
