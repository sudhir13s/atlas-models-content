---
id: model/tabpfn-2-5
type: model
slug: tabpfn-2-5
schema_version: 1.1
title: tabpfn_2_5
organization: organization/prior-labs
release_date: '2025-11-03'
release_year: 2025
release_month: 11
license_note: tabpfn-2.5-license-v1.1
model_category: structured-data
tasks:
- task/tabular-classification
summary: TabPFN-2.5 is a foundation model from Prior Labs for tabular data, meaning tables of rows and columns. Its card says it is a transformer that uses in-context learning to solve tabular prediction problems in a single forward pass, so it predicts without being trained again on each new table. The weights are released under the tabpfn-2.5-license-v1.1, which does not allow commercial or production use.
intended_use: The card names regression and classification on structured tabular data with up to 50,000 samples and 2,000 features. Out of scope are unstructured data such as text and images, and datasets above those sizes, which were not tested.
what_sets_it_apart: The card describes a transformer with alternating attention, similar to TabPFNv2, with 18 to 24 layers. TabPFN-2.5 was trained purely on synthetic tabular tasks, while a variant called Real-TabPFN-2.5 continued pre-training on real-world datasets, and the default classification checkpoint is fine-tuned on real data.
running_it: The card says to install the tabpfn package and to start with the default checkpoints, because the other available checkpoints are experimental and worse on average. They can be used in ensembles or hyperparameter searches, as the AutoTabPFN extension does.
limitations: The card says performance can degrade above 50,000 data points or 2,000 features. It adds that when the model is applied to high-risk cases, users should make sure their labelled data is free of biases.
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: tabular-classification
hub_signals:
  downloads_30d: 10117
  likes: 238
  last_modified: '2026-03-24'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Prior-Labs/tabpfn_2_5
aliases:
- Prior-Labs/tabpfn_2_5
lineage:
  parents: []
  children: []
quotes:
  summary: TabPFN-2.5 is a transformer-based foundation model that uses in-context-learning to solve tabular prediction problems in a forward pass.
  intended_use: Regression and classification tasks with ≤50 000 samples and ≤2000 features in structured tabular format.
  what_sets_it_apart: 'TabPFN-2.5: trained purely on synthetic tabular tasks'
  running_it: the other available checkpoints are experimental and worse on average, and we recommend to always start with the defaults.
  limitations: Performance can degrade when applied to >50000 data points and/or 2000 features.
tags:
- tabular-classification
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Prior-Labs/tabpfn_2_5
  title: Prior-Labs/tabpfn_2_5 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Prior-Labs/tabpfn_2_5/blob/main/config.json
  title: Prior-Labs/tabpfn_2_5 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2511.08667
  title: arXiv:2511.08667
  trust: 0.95
---

## Summary

TabPFN-2.5 is a foundation model from Prior Labs for tabular data, meaning tables of rows and columns. Its card says it is a transformer that uses in-context learning to solve tabular prediction problems in a single forward pass, so it predicts without being trained again on each new table. The weights are released under the tabpfn-2.5-license-v1.1, which does not allow commercial or production use.

## Intended Use

The card names regression and classification on structured tabular data with up to 50,000 samples and 2,000 features. Out of scope are unstructured data such as text and images, and datasets above those sizes, which were not tested.

## What Sets It Apart

The card describes a transformer with alternating attention, similar to TabPFNv2, with 18 to 24 layers. TabPFN-2.5 was trained purely on synthetic tabular tasks, while a variant called Real-TabPFN-2.5 continued pre-training on real-world datasets, and the default classification checkpoint is fine-tuned on real data.

## Running It

The card says to install the tabpfn package and to start with the default checkpoints, because the other available checkpoints are experimental and worse on average. They can be used in ensembles or hyperparameter searches, as the AutoTabPFN extension does.

## Limitations

The card says performance can degrade above 50,000 data points or 2,000 features. It adds that when the model is applied to high-risk cases, users should make sure their labelled data is free of biases.
