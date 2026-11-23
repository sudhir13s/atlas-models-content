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
pipeline:
  source: https://github.com/autogluon/autogluon/blob/master/tabular/src/autogluon/tabular/models/mitra/_internal/models/tab2d.py
  summary: The whole table goes in at once, the training rows with their known targets and the rows to predict with the target hidden. Every cell becomes a vector, and 12 layers alternate attention down the rows of each column with attention across the cells of each row. The vector at each predicted row's target cell becomes the predicted number.
  stages:
  - id: table
    role: input
    title: Table
    details:
    - training rows with targets
    - rows to predict, target hidden
    note: The training rows act as the examples the model learns from inside one forward pass, so there is no training step for a new table.
  - id: quantiles
    role: normalisation
    title: Quantile transform
    details:
    - per feature, from training rows
    note: Each feature value is replaced by its position among that feature's quantiles in the training rows.
  - id: embedding
    role: embedding
    title: Cell embedding
    details:
    - one vector per cell · width 512
    note: A linear layer turns each feature value and each known target into a vector. The hidden targets of the rows to predict get one learned mask vector instead.
  - id: row-attention
    role: attention
    title: Attention across rows
    details:
    - 4 heads · within each column
    frame: Layer
    repeat: 12
    note: Attention between rows, within each column. The rows to predict read the training rows; they do not read each other.
  - id: feed-forward-1
    role: feedForward
    title: Feed-forward
    details:
    - width 2048
    frame: Layer
  - id: feature-attention
    role: attention
    title: Attention across features
    details:
    - 4 heads · within each row
    frame: Layer
    note: Attention between the cells of one row, the features and the target, so each cell sees the rest of its row.
  - id: feed-forward-2
    role: feedForward
    title: Feed-forward
    details:
    - width 2048
    frame: Layer
  - id: norm
    role: normalisation
    title: Final LayerNorm
  - id: head
    role: outputHead
    title: Output head
    details:
    - linear · one number per row
    note: Only the vector at the target cell of each row to predict is used; a linear layer turns it into the predicted value.
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
- url: https://github.com/autogluon/autogluon/blob/master/tabular/src/autogluon/tabular/models/mitra/_internal/models/tab2d.py
  title: 'autogluon/tabular/models/mitra/_internal/models/tab2d.py: Mitra model code (AutoGluon)'
  trust: 0.9
---

## Summary

Mitra regressor is a tabular foundation model published on Hugging Face by the AutoGluon project. A tabular regression model predicts a number, such as a price, for each row of a table, and a foundation model here means one pre-trained once and then applied to new tables. The card says it is pre-trained purely on synthetic datasets sampled from a mix of random regressors, and it is a 12-layer Transformer of 72 million parameters.

## What Sets It Apart

The card says Mitra is pre-trained using an in-context learning paradigm, in which the model reads example rows as part of its input instead of being retrained for each table. It also stresses that all of its pre-training data is synthetic, drawn from a mix of random regressors.

## Running It

The card says to install AutoGluon with the mitra extra and call it through TabularPredictor with the MITRA hyperparameters. Its example sets fine_tune to False and fits on a sample of 1000 rows.
