---
id: model/tabpfn-mix-1-0-regressor
type: model
slug: tabpfn-mix-1-0-regressor
schema_version: 1.1
title: tabpfn-mix-1.0-regressor
organization: organization/autogluon
release_date: '2024-11-26'
release_year: 2024
release_month: 11
license: license/apache-2-0
model_category: structured-data
tasks:
- task/tabular-regression
parameters_active_b: 0.04
total_parameters: 38934017
summary: TabPFNMix regressor is a tabular foundation model, published under the autogluon account on Hugging Face, that predicts numeric values (regression) from spreadsheet-like tables. The card says it is pre-trained on purely synthetic datasets sampled from a mix of random regressors, and that it is a 12-layer encoder-decoder Transformer with 37 M parameters.
intended_use: The card shows using it through AutoGluon, an open-source machine learning library, with a minimal example of fine-tuning and inference on a regression problem. It gives no further list of intended or out-of-scope uses.
what_sets_it_apart: The card says the model is pre-trained with a strategy that uses in-context learning, similar to TabPFN and TabForestPFN, meaning the training rows are given to the model as context for its predictions. It also says all pre-training data was synthetic, sampled from a mix of random regressors.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 4
  kv_heads: 4
  head_size: 128
  hidden_size: 512
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: tabular-regression
hub_signals:
  downloads_30d: 1977
  likes: 16
  last_modified: '2024-11-27'
  observed_at: '2026-11-21'
  source: https://huggingface.co/autogluon/tabpfn-mix-1.0-regressor
aliases:
- autogluon/tabpfn-mix-1.0-regressor
lineage:
  parents: []
  children: []
quotes:
  summary: TabPFNMix regressor is a tabular foundation model that is pre-trained on purely synthetic datasets
  intended_use: A minimal example showing how to perform fine-tuning and inference using TabPFNMix regressor
  what_sets_it_apart: We use a pre-training strategy incorporating in-context learning, similar to that used by TabPFN and TabForestPFN.
tags:
- tabular-regression
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/autogluon/tabpfn-mix-1.0-regressor
  title: autogluon/tabpfn-mix-1.0-regressor — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/autogluon/tabpfn-mix-1.0-regressor/blob/main/config.json
  title: autogluon/tabpfn-mix-1.0-regressor — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2003.06505
  title: arXiv:2003.06505
  trust: 0.95
- url: https://arxiv.org/abs/2207.01848
  title: arXiv:2207.01848
  trust: 0.95
- url: https://arxiv.org/abs/2405.13396
  title: arXiv:2405.13396
  trust: 0.95
---

## Summary

TabPFNMix regressor is a tabular foundation model, published under the autogluon account on Hugging Face, that predicts numeric values (regression) from spreadsheet-like tables. The card says it is pre-trained on purely synthetic datasets sampled from a mix of random regressors, and that it is a 12-layer encoder-decoder Transformer with 37 M parameters.

## Intended Use

The card shows using it through AutoGluon, an open-source machine learning library, with a minimal example of fine-tuning and inference on a regression problem. It gives no further list of intended or out-of-scope uses.

## What Sets It Apart

The card says the model is pre-trained with a strategy that uses in-context learning, similar to TabPFN and TabForestPFN, meaning the training rows are given to the model as context for its predictions. It also says all pre-training data was synthetic, sampled from a mix of random regressors.
