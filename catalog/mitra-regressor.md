---
id: model/mitra-regressor
type: model
slug: mitra-regressor
schema_version: 1.1
title: mitra-regressor
organization: organization/autogluon
release_date: '2025-06-22'
release_year: 2025
release_month: 6
license: license/apache-2-0
model_category: structured-data
tasks:
- task/tabular-regression
parameters_active_b: 0.08
total_parameters: 75661313
summary: Mitra regressor is a tabular foundation model published on Hugging Face by the AutoGluon project. A tabular regression model predicts a number, such as a price, for each row of a table, and a foundation model here means one pre-trained once and then applied to new tables. The card says it is pre-trained purely on synthetic datasets sampled from a mix of random regressors, and it is a 12-layer Transformer of 72 million parameters.
what_sets_it_apart: The card says Mitra is pre-trained using an in-context learning paradigm, in which the model reads example rows as part of its input instead of being retrained for each table. It also stresses that all of its pre-training data is synthetic, drawn from a mix of random regressors.
running_it: The card says to install AutoGluon with the mitra extra and call it through TabularPredictor with the MITRA hyperparameters. Its example sets fine_tune to False and fits on a sample of 1000 rows.
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
  downloads_30d: 186891
  likes: 33
  last_modified: '2025-11-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/autogluon/mitra-regressor
aliases:
- autogluon/mitra-regressor
lineage:
  parents: []
  children: []
quotes:
  summary: Mitra regressor is a tabular foundation model that is pre-trained on purely synthetic datasets sampled from a mix of random regressors.
  what_sets_it_apart: Mitra is based on a 12-layer Transformer of 72 M parameters, pre-trained by incorporating an in-context learning paradigm.
  running_it: 'To use Mitra regressor, install AutoGluon by running:'
tags:
- tabular-regression
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/autogluon/mitra-regressor
  title: autogluon/mitra-regressor — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/autogluon/mitra-regressor/blob/main/config.json
  title: autogluon/mitra-regressor — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2510.21204
  title: arXiv:2510.21204
  trust: 0.95
---

## Summary

Mitra regressor is a tabular foundation model published on Hugging Face by the AutoGluon project. A tabular regression model predicts a number, such as a price, for each row of a table, and a foundation model here means one pre-trained once and then applied to new tables. The card says it is pre-trained purely on synthetic datasets sampled from a mix of random regressors, and it is a 12-layer Transformer of 72 million parameters.

## What Sets It Apart

The card says Mitra is pre-trained using an in-context learning paradigm, in which the model reads example rows as part of its input instead of being retrained for each table. It also stresses that all of its pre-training data is synthetic, drawn from a mix of random regressors.

## Running It

The card says to install AutoGluon with the mitra extra and call it through TabularPredictor with the MITRA hyperparameters. Its example sets fine_tune to False and fits on a sample of 1000 rows.
