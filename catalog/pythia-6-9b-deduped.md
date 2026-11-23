---
id: model/pythia-6-9b-deduped
type: model
slug: pythia-6-9b-deduped
schema_version: 1.1
title: pythia-6.9b-deduped
organization: organization/eleutherai
release_date: '2023-02-25'
release_year: 2023
release_month: 2
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 6.86
total_parameters: 6857302016
context_window_tokens: 2048
summary: Pythia-6.9B-deduped is an English language model from EleutherAI, one of the Pythia suite of models built to support research into how language models work. It was trained on the Pile after that dataset was globally deduplicated, meaning repeated documents were removed. The card says it only predicts the next token, so it does not answer prompts the way a chat product does.
intended_use: The card names research on the behavior, functionality and limitations of large language models as the primary use, with intermediate checkpoints stored as branches for controlled experiments. Fine-tuning for deployment is allowed under the Apache 2.0 license, after the user's own risk and bias assessment. The card lists as out of scope any deployment, human-facing interaction, translation or text in other languages, and chatbot or genre-writing use.
what_sets_it_apart: The card describes a suite of two sets of eight models, from 70M to 12B parameters, one set trained on the Pile and one on the deduplicated Pile. All sizes saw the same data in the same order, and 154 intermediate checkpoints per model are published. The authors say they did not make downstream performance a design goal but that the models match or exceed similar-sized OPT and GPT-Neo models.
limitations: The card says never to rely on the model for factually accurate output. It notes that the Pile contains profanity and offensive text, so the model may produce undesirable text even when the prompt is not offensive.
fingerprint:
  attention_type: MHA
  num_layers: 32
  attention_heads: 32
  kv_heads: 32
  head_size: 128
  hidden_size: 4096
  intermediate_size: 16384
  context_window_tokens: 2048
  vocabulary_size: 50432
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: pre
  stack: decoder
  parallel_sublayers: separate-norms
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 7025
  likes: 7
  last_modified: '2023-06-08'
  observed_at: '2026-11-20'
  source: https://huggingface.co/EleutherAI/pythia-6.9b-deduped
aliases:
- EleutherAI/pythia-6.9b-deduped
- pythia-6.9b-deduped
lineage:
  parents: []
  children: []
quotes:
  summary: The *Pythia Scaling Suite* is a collection of models developed to facilitate
  intended_use: The primary intended use of Pythia is research on the behavior, functionality,
  what_sets_it_apart: All 8 model sizes are trained on the exact
  limitations: Never rely on Pythia-6.9B-deduped to produce factually accurate
tags:
- llm
- text-generation
- gpt-neox
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/EleutherAI/pythia-6.9b-deduped
  title: EleutherAI/pythia-6.9b-deduped — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/EleutherAI/pythia-6.9b-deduped/blob/main/config.json
  title: EleutherAI/pythia-6.9b-deduped — config.json
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

Pythia-6.9B-deduped is an English language model from EleutherAI, one of the Pythia suite of models built to support research into how language models work. It was trained on the Pile after that dataset was globally deduplicated, meaning repeated documents were removed. The card says it only predicts the next token, so it does not answer prompts the way a chat product does.

## Intended Use

The card names research on the behavior, functionality and limitations of large language models as the primary use, with intermediate checkpoints stored as branches for controlled experiments. Fine-tuning for deployment is allowed under the Apache 2.0 license, after the user's own risk and bias assessment. The card lists as out of scope any deployment, human-facing interaction, translation or text in other languages, and chatbot or genre-writing use.

## What Sets It Apart

The card describes a suite of two sets of eight models, from 70M to 12B parameters, one set trained on the Pile and one on the deduplicated Pile. All sizes saw the same data in the same order, and 154 intermediate checkpoints per model are published. The authors say they did not make downstream performance a design goal but that the models match or exceed similar-sized OPT and GPT-Neo models.

## Limitations

The card says never to rely on the model for factually accurate output. It notes that the Pile contains profanity and offensive text, so the model may produce undesirable text even when the prompt is not offensive.
