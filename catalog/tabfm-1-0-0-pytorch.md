---
id: model/tabfm-1-0-0-pytorch
type: model
slug: tabfm-1-0-0-pytorch
schema_version: 1.1
title: tabfm-1.0.0-pytorch
organization: organization/google-deepmind
release_date: '2026-06-29'
release_year: 2026
release_month: 6
license_note: tabfm-non-commercial-v1.0
model_category: structured-data
tasks:
- task/tabular-classification
summary: 'TabFM 1.0.0 is a tabular foundation model from Google Research. Tabular data is rows and columns, like a spreadsheet, and the model does classification and regression on tables with numerical and categorical columns. The card says it needs no fine-tuning or hyperparameter search: the training rows are passed in as context and predictions come out in a single forward pass. This repository holds the PyTorch weights.'
intended_use: The card lists tabular data with numerical or categorical columns, binary and multiclass classification with up to 10 classes, regression on continuous targets, and zero-shot use with no dataset-specific training. It lists as not intended images, audio, video or raw text, more than 10 output classes, tasks needing task-specific fine-tuning, non-tabular structured data, and commercial use, which its license does not permit.
what_sets_it_apart: 'The card describes alternating column and row attention: a column step that embeds each cell, a row step that compresses each row into one vector, and a 24-block causal transformer that treats the training rows as context and outputs predictions for test rows. It says the model was trained on hundreds of millions of synthetic datasets generated from structural causal models, chosen because of the scarcity of diverse open tabular datasets and to avoid privacy and licensing concerns.'
limitations: The card gives a hard limit of 10 classes, says memory use grows with the number of training rows because all rows are passed as context, and says it is optimized for tables of up to 500 features. It states that performance is not guaranteed to match fine-tuned models on every dataset, that behavior on real-world domains and minority groups is not fully characterised, and that it is not an officially supported Google product.
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: tabular-classification
  library: tabfm
hub_signals:
  downloads_30d: 39191
  likes: 507
  last_modified: '2026-07-04'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/tabfm-1.0.0-pytorch
aliases:
- google/tabfm-1.0.0-pytorch
lineage:
  parents: []
  children: []
quotes:
  summary: TabFM is a zero-shot tabular foundation model from Google Research.
  intended_use: Binary and multiclass classification (up to 10 classes)
  what_sets_it_apart: TabFM uses alternating row and column attention to capture both feature interactions
  limitations: Memory usage scales with the number of training rows (all rows are passed as context)
tags:
- tabular-classification
- tabfm
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/tabfm-1.0.0-pytorch
  title: google/tabfm-1.0.0-pytorch — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/tabfm-1.0.0-pytorch/blob/main/config.json
  title: google/tabfm-1.0.0-pytorch — config.json
  trust: 0.9
---

## Summary

TabFM 1.0.0 is a tabular foundation model from Google Research. Tabular data is rows and columns, like a spreadsheet, and the model does classification and regression on tables with numerical and categorical columns. The card says it needs no fine-tuning or hyperparameter search: the training rows are passed in as context and predictions come out in a single forward pass. This repository holds the PyTorch weights.

## Intended Use

The card lists tabular data with numerical or categorical columns, binary and multiclass classification with up to 10 classes, regression on continuous targets, and zero-shot use with no dataset-specific training. It lists as not intended images, audio, video or raw text, more than 10 output classes, tasks needing task-specific fine-tuning, non-tabular structured data, and commercial use, which its license does not permit.

## What Sets It Apart

The card describes alternating column and row attention: a column step that embeds each cell, a row step that compresses each row into one vector, and a 24-block causal transformer that treats the training rows as context and outputs predictions for test rows. It says the model was trained on hundreds of millions of synthetic datasets generated from structural causal models, chosen because of the scarcity of diverse open tabular datasets and to avoid privacy and licensing concerns.

## Limitations

The card gives a hard limit of 10 classes, says memory use grows with the number of training rows because all rows are passed as context, and says it is optimized for tables of up to 500 features. It states that performance is not guaranteed to match fine-tuned models on every dataset, that behavior on real-world domains and minority groups is not fully characterised, and that it is not an officially supported Google product.
