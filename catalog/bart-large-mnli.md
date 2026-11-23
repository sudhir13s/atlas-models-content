---
id: model/bart-large-mnli
type: model
slug: bart-large-mnli
schema_version: 3.0
title: bart-large-mnli
organization: organization/meta
license: license/mit
model_category: language
model_kind: transformer-language
tasks:
- task/zero-shot-classification
parameters_active_b: 0.41
total_parameters: 407344133
context_window_tokens: 1024
base_models:
- name: facebook/bart-large
  address: https://huggingface.co/facebook/bart-large
summary: bart-large-mnli is a text classification model published on Hugging Face under the facebook organization. Its card says it is the bart-large checkpoint (a sequence-to-sequence language model) trained further on MultiNLI (MNLI), a dataset of sentence pairs labelled by whether the second sentence follows from the first. It is mainly used to sort a piece of text into categories you choose at run time, without training a new model.
intended_use: The card presents the model for zero-shot text classification, meaning classifying text into labels the model was never explicitly trained on. When more than one label can be correct, it says to pass multi_label=True so each label is scored independently.
what_sets_it_apart: The card explains that it uses a natural language inference (NLI) trick proposed by Yin et al. The text to classify is posed as a premise, a hypothesis such as "This text is about politics." is built from each candidate label, and the model's probabilities for entailment (the premise supports the hypothesis) and contradiction are converted into label probabilities.
fingerprint:
  attention_type: MHA
  num_layers: 12
  decoder_layers: 12
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  intermediate_size: 4096
  vocabulary_size: 50265
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: post
  stack: encoder-decoder
  embedding_norm: true
  positional_encoding: Learned
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: zero-shot-classification
  library: transformers
hub_signals:
  downloads_30d: 3653291
  likes: 1641
  last_modified: '2023-09-05'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/bart-large-mnli
aliases:
- facebook/bart-large-mnli
lineage:
  parents: []
  children: []
quotes:
  summary: This is the checkpoint for [bart-large](https://huggingface.co/facebook/bart-large) after being trained on the [MultiNLI (MNLI)](https://huggingface.co/datasets/multi_nli) dataset.
  intended_use: 'If more than one candidate label can be correct, pass `multi_label=True` to calculate each class independently:'
  what_sets_it_apart: The method works by posing the sequence to be classified as the NLI premise and to construct a hypothesis from each candidate label.
tags:
- bart
updated: '2026-11-23'
sources:
- url: https://huggingface.co/facebook/bart-large-mnli
  title: facebook/bart-large-mnli — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/bart-large-mnli/blob/main/config.json
  title: facebook/bart-large-mnli — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1910.13461
  title: arXiv:1910.13461
  trust: 0.95
- url: https://arxiv.org/abs/1909.00161
  title: arXiv:1909.00161
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bart/modeling_bart.py
  title: 'transformers/models/bart/modeling_bart.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


