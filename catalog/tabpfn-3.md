---
id: model/tabpfn-3
type: model
slug: tabpfn-3
schema_version: 1.1
title: tabpfn_3
organization: organization/prior-labs
release_date: '2026-05-05'
release_year: 2026
release_month: 5
license_note: tabpfn-3-license-v1.0
model_category: structured-data
tasks:
- task/tabular-classification
summary: 'TabPFN-3 is a tabular prediction model from Prior Labs. Tabular data is data in rows and columns, such as a spreadsheet. The card describes a transformer-based foundation model that uses in-context learning: you give it your labeled rows and it predicts for new rows in a single forward pass, without training a separate model for each dataset.'
intended_use: The card names regression and classification on structured tabular data with up to 1 million samples and 2000 features or fewer. It says it is not suitable for unstructured data such as text or images, and that its license prohibits commercial or production use of the model and its outputs.
what_sets_it_apart: The card says TabPFN-3 was trained purely on synthetic tabular tasks, so its pretraining has no dataset leakage. It describes a multi-stage transformer with 24 main layers and several specialized checkpoints, for example for binary classification, multiclass classification, time series and inputs outside the training distribution.
limitations: The card says performance can degrade above 1 million data points or above 2000 features. It also says that in high-risk uses, users should make sure the labelled data is free of biases.
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: tabular-classification
hub_signals:
  downloads_30d: 9231
  likes: 266
  last_modified: '2026-07-04'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Prior-Labs/tabpfn_3
aliases:
- Prior-Labs/tabpfn_3
lineage:
  parents: []
  children: []
quotes:
  summary: TabPFN-3 is a transformer-based foundation model that uses in-context-learning to solve tabular prediction problems in a forward pass.
  intended_use: Regression and classification tasks with up to 1M samples and ≤2000 features in structured tabular format.
  what_sets_it_apart: TabPFN-3 is trained purely on synthetic tabular tasks.
  limitations: Performance can degrade when applied to >1M data points and/or >2000 features.
tags:
- tabular-classification
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Prior-Labs/tabpfn_3
  title: Prior-Labs/tabpfn_3 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Prior-Labs/tabpfn_3/blob/main/config.json
  title: Prior-Labs/tabpfn_3 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2605.13986
  title: arXiv:2605.13986
  trust: 0.95
---

## Summary

TabPFN-3 is a tabular prediction model from Prior Labs. Tabular data is data in rows and columns, such as a spreadsheet. The card describes a transformer-based foundation model that uses in-context learning: you give it your labeled rows and it predicts for new rows in a single forward pass, without training a separate model for each dataset.

## Intended Use

The card names regression and classification on structured tabular data with up to 1 million samples and 2000 features or fewer. It says it is not suitable for unstructured data such as text or images, and that its license prohibits commercial or production use of the model and its outputs.

## What Sets It Apart

The card says TabPFN-3 was trained purely on synthetic tabular tasks, so its pretraining has no dataset leakage. It describes a multi-stage transformer with 24 main layers and several specialized checkpoints, for example for binary classification, multiclass classification, time series and inputs outside the training distribution.

## Limitations

The card says performance can degrade above 1 million data points or above 2000 features. It also says that in high-risk uses, users should make sure the labelled data is free of biases.
