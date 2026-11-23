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
pipeline:
  source: https://www.nature.com/articles/s41586-024-08328-6
  summary: Every cell of the table gets its own vector, with features encoded two at a time and the target as one more column. Each layer attends across the features of a row, then down the samples of a column, where test rows read only the training rows. The output for each test row is a distribution over 5,000 value buckets.
  stages:
  - id: table
    role: input
    title: Table
    details:
    - training rows with targets
    - test rows
  - id: normalise
    role: normalisation
    title: Z-normalisation
    details:
    - per feature, over the training set
    note: Categorical values are turned into integers, and each feature is scaled with its mean and standard deviation over the training set.
  - id: encoder
    role: embedding
    title: Feature encoder
    details:
    - linear · 2 features per position
    - + random feature embedding
    note: In the default model one position encodes two features of a row. A random embedding per feature, passed through a learned linear layer, is added so features with the same statistics can be told apart.
  - id: target-column
    role: embedding
    title: Target as extra column
    details:
    - known on training rows only
    note: The training targets are embedded by a linear layer and added as the last column; test rows carry no known target.
  - id: feature-attention
    role: attention
    title: Attention across features
    details:
    - within each row
    frame: Layer
    note: Each cell attends to the other features of its own row. Every sublayer is followed by a residual addition and a layer norm.
  - id: sample-attention
    role: attention
    title: Attention across samples
    details:
    - test rows read training rows only
    frame: Layer
    note: Each cell attends to the same feature in other rows. Test rows attend only to training rows, so they never influence each other or the training rows.
  - id: mlp
    role: feedForward
    title: Feed-forward
    details:
    - per cell
    frame: Layer
  - id: head
    role: outputHead
    title: Output head
    details:
    - two linear layers · target cell
    note: Reads the vector in the target column of each test row.
  - id: buckets
    role: outputHead
    title: Distribution over values
    details:
    - 5,000 buckets
    note: The target range is split into 5,000 buckets at quantiles of the pre-training data, and the model gives a probability per bucket. The normalisation of the target is undone by moving the bucket borders.
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
- url: https://www.nature.com/articles/s41586-024-08328-6
  title: Accurate predictions on small data with a tabular foundation model (Nature, 2025)
  trust: 0.95
- url: https://github.com/PriorLabs/TabPFN/blob/main/src/tabpfn/architectures/tabpfn_v2.py
  title: 'TabPFN/src/tabpfn/architectures/tabpfn_v2.py: TabPFN v2 architecture code (Prior Labs)'
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
