---
id: model/opt-125m
type: model
slug: opt-125m
schema_version: 1.1
title: opt-125m
organization: organization/meta
release_date: '2022-05-11'
release_year: 2022
release_month: 5
license_note: other
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
context_window_tokens: 2048
summary: OPT-125M is the smallest model of OPT (Open Pre-trained Transformer), a suite of decoder-only language models from Meta AI that ranges from 125 million to 175 billion parameters. It was first released on May 3rd 2022. It generates text by predicting the next word, and its model card was written by the Hugging Face team from the official card.
intended_use: The card says this pretrained-only model can be prompted to evaluate downstream tasks and to generate text, and that it can be fine-tuned on a downstream task. The paper excerpt in the card says the suite was shared so that researchers can study large language models, which were otherwise open to only a few well-resourced labs.
what_sets_it_apart: The card says OPT was pretrained predominantly on English text, with a small amount of non-English data from CommonCrawl, using a causal language modeling objective. It lists a training set of 180 billion tokens from five filtered datasets, among them BookCorpus, The Pile and Reddit data.
limitations: The card says that because the training data contains a lot of unfiltered content from the internet, the model is strongly biased, and that this bias also affects fine-tuned versions. It quotes Meta AI's note that OPT-175B has limits in bias, safety, generation diversity and hallucination.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 2048
  vocabulary_size: 50272
  activation_function: relu
  positional_encoding: Learned
  norm_placement: pre
  stack: decoder
filters:
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 4497895
  likes: 334
  last_modified: '2023-09-15'
  observed_at: '2026-11-20'
  source: https://huggingface.co/facebook/opt-125m
aliases:
- facebook/opt-125m
lineage:
  parents: []
  children: []
quotes:
  summary: The pretrained-only model can be used for prompting for evaluation of downstream tasks as well as text generation.
  intended_use: In addition, the model can be fine-tuned on a downstream task using the
  what_sets_it_apart: OPT was predominantly pretrained with English text, but a small amount of non-English data is still present within the training corpus via CommonCrawl.
  limitations: 'given that the training data used for this model contains a lot of unfiltered content from the internet, which is far from neutral the model is strongly biased :'
tags:
- llm
- text-generation
- opt
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/facebook/opt-125m
  title: facebook/opt-125m — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/opt-125m/blob/main/config.json
  title: facebook/opt-125m — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2205.01068
  title: arXiv:2205.01068
  trust: 0.95
- url: https://arxiv.org/abs/2005.14165
  title: arXiv:2005.14165
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/opt/modeling_opt.py
  title: 'transformers/models/opt/modeling_opt.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

OPT-125M is the smallest model of OPT (Open Pre-trained Transformer), a suite of decoder-only language models from Meta AI that ranges from 125 million to 175 billion parameters. It was first released on May 3rd 2022. It generates text by predicting the next word, and its model card was written by the Hugging Face team from the official card.

## Intended Use

The card says this pretrained-only model can be prompted to evaluate downstream tasks and to generate text, and that it can be fine-tuned on a downstream task. The paper excerpt in the card says the suite was shared so that researchers can study large language models, which were otherwise open to only a few well-resourced labs.

## What Sets It Apart

The card says OPT was pretrained predominantly on English text, with a small amount of non-English data from CommonCrawl, using a causal language modeling objective. It lists a training set of 180 billion tokens from five filtered datasets, among them BookCorpus, The Pile and Reddit data.

## Limitations

The card says that because the training data contains a lot of unfiltered content from the internet, the model is strongly biased, and that this bias also affects fine-tuned versions. It quotes Meta AI's note that OPT-175B has limits in bias, safety, generation diversity and hallucination.
