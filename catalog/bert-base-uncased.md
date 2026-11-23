---
id: model/bert-base-uncased
type: model
slug: bert-base-uncased
schema_version: 1.1
title: bert-base-uncased
organization: organization/google-bert
license: license/apache-2-0
model_category: language
tasks:
- task/fill-mask
parameters_active_b: 0.11
total_parameters: 110106428
context_window_tokens: 512
summary: BERT base uncased is an English language model, first released in the google-research/bert project, that reads text and turns it into numeric features rather than writing new text. It was pretrained by hiding words in a sentence and learning to predict them, a task called masked language modeling. The card says the model is uncased, so it treats "english" and "English" as the same word, and that the Hugging Face team wrote the card.
intended_use: The card says the raw model can be used for masked language modeling or next sentence prediction, but that it is mostly meant to be fine-tuned on a downstream task. It names tasks that use the whole sentence to decide, such as sequence classification, token classification and question answering. For text generation the card points to models such as GPT2 instead.
what_sets_it_apart: The card describes self-supervised pretraining on raw English text, with no human labels. Two objectives were used. In masked language modeling the model hides 15% of the words and predicts them, which lets it learn from both the left and the right of a word. In next sentence prediction it judges whether two sentences followed each other in the original text. The card lists the bookcorpus and wikipedia datasets.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 512
  vocabulary_size: 30522
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: post
  stack: encoder
  embedding_norm: true
  positional_encoding: Learned
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: fill-mask
  library: transformers
hub_signals:
  downloads_30d: 33456723
  likes: 3084
  last_modified: '2024-02-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google-bert/bert-base-uncased
aliases:
- google-bert/bert-base-uncased
lineage:
  parents: []
  children: []
quotes:
  summary: Pretrained model on English language using a masked language modeling (MLM) objective.
  intended_use: You can use the raw model for either masked language modeling or next sentence prediction
  what_sets_it_apart: the model randomly masks 15% of the words in the input
tags:
- llm
- fill-mask
- bert
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google-bert/bert-base-uncased
  title: google-bert/bert-base-uncased — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
  title: google-bert/bert-base-uncased — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1810.04805
  title: arXiv:1810.04805
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bert/modeling_bert.py
  title: 'transformers/models/bert/modeling_bert.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

BERT base uncased is an English language model, first released in the google-research/bert project, that reads text and turns it into numeric features rather than writing new text. It was pretrained by hiding words in a sentence and learning to predict them, a task called masked language modeling. The card says the model is uncased, so it treats "english" and "English" as the same word, and that the Hugging Face team wrote the card.

## Intended Use

The card says the raw model can be used for masked language modeling or next sentence prediction, but that it is mostly meant to be fine-tuned on a downstream task. It names tasks that use the whole sentence to decide, such as sequence classification, token classification and question answering. For text generation the card points to models such as GPT2 instead.

## What Sets It Apart

The card describes self-supervised pretraining on raw English text, with no human labels. Two objectives were used. In masked language modeling the model hides 15% of the words and predicts them, which lets it learn from both the left and the right of a word. In next sentence prediction it judges whether two sentences followed each other in the original text. The card lists the bookcorpus and wikipedia datasets.
