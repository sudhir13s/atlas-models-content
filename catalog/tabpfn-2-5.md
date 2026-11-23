---
id: model/tabpfn-2-5
type: model
slug: tabpfn-2-5
schema_version: 3.0
title: tabpfn_2_5
organization: organization/prior-labs
release_date: '2025-11-03'
license_note: tabpfn-2.5-license-v1.1
model_category: structured-data
model_kind: tabular-time-series
tasks:
- task/tabular-classification
summary: TabPFN-2.5 is a foundation model from Prior Labs for tabular data, meaning tables of rows and columns. Its card says it is a transformer that uses in-context learning to solve tabular prediction problems in a single forward pass, so it predicts without being trained again on each new table. The weights are released under the tabpfn-2.5-license-v1.1, which does not allow commercial or production use.
intended_use: The card names regression and classification on structured tabular data with up to 50,000 samples and 2,000 features. Out of scope are unstructured data such as text and images, and datasets above those sizes, which were not tested.
what_sets_it_apart: The card describes a transformer with alternating attention, similar to TabPFNv2, with 18 to 24 layers. TabPFN-2.5 was trained purely on synthetic tabular tasks, while a variant called Real-TabPFN-2.5 continued pre-training on real-world datasets, and the default classification checkpoint is fine-tuned on real data.
running_it: The card says to install the tabpfn package and to start with the default checkpoints, because the other available checkpoints are experimental and worse on average. They can be used in ensembles or hyperparameter searches, as the AutoTabPFN extension does.
limitations: The card says performance can degrade above 50,000 data points or 2,000 features. It adds that when the model is applied to high-risk cases, users should make sure their labelled data is free of biases.
pipeline:
  source: https://github.com/PriorLabs/TabPFN/blob/main/src/tabpfn/architectures/tabpfn_v2_5.py
  summary: The table is cleaned and scaled, features are embedded in groups of three, and the target becomes one more column. With 64 learned "thinking" rows added, each layer attends across the cells of every row and then down every column, where test rows read only the thinking and training rows. A small head turns each test row's target cell into the prediction.
  stages:
  - id: table
    role: input
    title: Table
    details:
    - training rows with targets
    - test rows
    note: The card names up to 50,000 rows and 2,000 features as the tested range.
  - id: preprocess
    role: normalisation
    title: Feature preprocessing
    details:
    - impute · standardise per feature
    note: Constant features are removed, missing or infinite values are replaced by the training mean and flagged, and each feature is standardised with statistics from the training rows.
  - id: group-embedding
    role: embedding
    title: Feature-group embedding
    details:
    - 3 features per group
    - + a fixed embedding per column
    note: Each group of three features, with its missing-value flags, becomes one cell vector. The paper says the regression model uses a two-layer MLP here instead of a linear layer.
  - id: target-column
    role: embedding
    title: Target as extra column
    details:
    - known on training rows only
    note: The target value, with a flag for missing values, is embedded and placed as the last cell of every row; test rows carry no known target.
  - id: thinking-rows
    role: embedding
    title: Add thinking rows
    details:
    - 64 learned rows
    note: Rows learned in pre-training are placed before the data. The paper says they add computing capacity and can act as attention sinks.
  - id: feature-attention
    role: attention
    title: Attention across a row
    details:
    - between the cells of one row
    - 24 layers classifier · 18 regressor
    frame: Layer
    note: Attention between the cells of one row, so feature groups and the target exchange information. The paper gives 24 layers for the classification model and 18 for the regression model.
  - id: row-attention
    role: attention
    title: Attention down a column
    details:
    - reads thinking + training rows
    frame: Layer
    note: Attention between rows within one column. Thinking and training rows attend to each other; test rows attend only to them, never to other test rows.
  - id: mlp
    role: feedForward
    title: Feed-forward
    details:
    - norm after each part
    frame: Layer
  - id: head
    role: outputHead
    title: Output head
    details:
    - two linear layers · GELU
    note: Reads the vector in the target cell of each test row and gives that row's prediction.
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
tags: []
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
- url: https://github.com/PriorLabs/TabPFN/blob/main/src/tabpfn/architectures/tabpfn_v2_5.py
  title: 'TabPFN/src/tabpfn/architectures/tabpfn_v2_5.py: TabPFN-2.5 architecture code (Prior Labs)'
  trust: 0.9
---


