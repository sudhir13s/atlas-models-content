---
id: model/tabpfn-v2-reg
type: model
slug: tabpfn-v2-reg
schema_version: 1.1
title: TabPFN-v2-reg
organization: organization/prior-labs
release_date: '2025-01-04'
release_year: 2025
release_month: 1
license_note: priorlabs-1-1
model_category: structured-data
tasks:
- task/tabular-regression
summary: TabPFN v2 is a foundation model for tabular data from Prior Labs, here in its regression version, which predicts a number for each row of a table. The card describes a transformer-based model using prior-data based learning, which it says aims to perform well on small tabular regression tasks without task-specific training. The card says the paper was published in Nature in January 2025.
intended_use: 'The card presents it for small tabular regression tasks. It lists two limits: it is not designed for very large datasets and it is not suitable for non-tabular data formats.'
what_sets_it_apart: The card says the model leverages prior-data based learning and needs no task-specific training, so it predicts for a new table directly instead of being retrained on it.
running_it: The card lists Python 3.9 or later, PyTorch 2.1 or later and scikit-learn 1.0 or later, with 16GB or more of RAM and a CPU, and says a GPU is optional. It is installed with pip install tabpfn.
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: tabular-regression
  library: tabpfn
hub_signals:
  downloads_30d: 36540
  likes: 67
  last_modified: '2026-10-28'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Prior-Labs/TabPFN-v2-reg
aliases:
- Prior-Labs/TabPFN-v2-reg
lineage:
  parents: []
  children: []
quotes:
  summary: TabPFN is a transformer-based foundation model for tabular data that leverages prior-data based learning
  intended_use: Not designed for very large datasets
  what_sets_it_apart: without requiring task-specific training.
  running_it: 16GB+ RAM, CPU (GPU optional)
tags:
- tabular-regression
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Prior-Labs/TabPFN-v2-reg
  title: Prior-Labs/TabPFN-v2-reg — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Prior-Labs/TabPFN-v2-reg/blob/main/config.json
  title: Prior-Labs/TabPFN-v2-reg — config.json
  trust: 0.9
---

## Summary

TabPFN v2 is a foundation model for tabular data from Prior Labs, here in its regression version, which predicts a number for each row of a table. The card describes a transformer-based model using prior-data based learning, which it says aims to perform well on small tabular regression tasks without task-specific training. The card says the paper was published in Nature in January 2025.

## Intended Use

The card presents it for small tabular regression tasks. It lists two limits: it is not designed for very large datasets and it is not suitable for non-tabular data formats.

## What Sets It Apart

The card says the model leverages prior-data based learning and needs no task-specific training, so it predicts for a new table directly instead of being retrained on it.

## Running It

The card lists Python 3.9 or later, PyTorch 2.1 or later and scikit-learn 1.0 or later, with 16GB or more of RAM and a CPU, and says a GPU is optional. It is installed with pip install tabpfn.
