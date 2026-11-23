---
id: model/tabpfn-3
type: model
slug: tabpfn-3
schema_version: 3.0
title: tabpfn_3
organization: organization/prior-labs
release_date: '2026-05-05'
license_note: tabpfn-3-license-v1.0
model_category: structured-data
model_kind: tabular-time-series
tasks:
- task/tabular-classification
summary: 'TabPFN-3 is a tabular prediction model from Prior Labs. Tabular data is data in rows and columns, such as a spreadsheet. The card describes a transformer-based foundation model that uses in-context learning: you give it your labeled rows and it predicts for new rows in a single forward pass, without training a separate model for each dataset.'
intended_use: The card names regression and classification on structured tabular data with up to 1 million samples and 2000 features or fewer. It says it is not suitable for unstructured data such as text or images, and that its license prohibits commercial or production use of the model and its outputs.
what_sets_it_apart: The card says TabPFN-3 was trained purely on synthetic tabular tasks, so its pretraining has no dataset leakage. It describes a multi-stage transformer with 24 main layers and several specialized checkpoints, for example for binary classification, multiclass classification, time series and inputs outside the training distribution.
limitations: The card says performance can degrade above 1 million data points or above 2000 features. It also says that in high-risk uses, users should make sure the labelled data is free of biases.
pipeline:
  source: https://arxiv.org/abs/2605.13986
  summary: The table is turned into one vector per row in two stages, one down each column and one across each row. A 24-block transformer then lets test rows read the labelled training rows, and a retrieval decoder turns each test row into class scores by attending over the training rows' labels. The figure shows the classifier.
  stages:
  - id: table
    role: input
    title: Table
    details:
    - training rows with labels
    - test rows
  - id: groups
    role: tokenizer
    title: Feature triplets
    details:
    - each feature + 2 shifted neighbours
    - missing-value flag per cell
    note: Each feature is grouped with two cyclically shifted neighbouring features. A binary flag marks cells that are missing.
  - id: cell-embedding
    role: embedding
    title: Cell embedding
    details:
    - linear · width 128
    - + label embedding on training rows
    note: Each triplet is mapped to the model width by a learned linear layer. On training rows a target-aware embedding is added.
  - id: column-stage
    role: attention
    title: Induced self-attention
    details:
    - 8 heads · 128 inducing points
    frame: Column stage
    repeat: 3
    note: Feature distribution embedding. Each column is embedded on its own by attention through 128 learned inducing points, which captures column statistics without full attention between all rows.
  - id: row-stage
    role: attention
    title: Attention across a row
    details:
    - 8 heads · 4 CLS tokens · RoPE
    frame: Row stage
    repeat: 3
    note: Feature aggregation. Four learned summary tokens (CLS tokens) and the cells of one row attend to each other, with rotary position embeddings (RoPE).
  - id: row-vector
    role: projector
    title: Row vector
    details:
    - 4 CLS tokens × 128 = width 512
    note: The four summary tokens are joined, so every row becomes one vector whatever the number of features.
  - id: add-labels
    role: embedding
    title: Add label embedding
    details:
    - training rows only
    note: Learned label embeddings, initialised to be orthogonal, are added to the training-row vectors.
  - id: icl-attention
    role: attention
    title: Attention + feed-forward
    details:
    - 8 heads · width 512
    - test rows read training rows only
    frame: ICL transformer
    repeat: 24
    note: In-context learning (ICL). Training rows attend to each other with full multi-head attention; test rows attend only to training rows, through a single key-value head (multi-query attention).
  - id: decoder
    role: outputHead
    title: Many-class decoder
    details:
    - 6 heads · retrieval of labels
    note: Each test row attends over the final training-row vectors and takes the attention-weighted average of their one-hot labels as class probabilities. The regression model uses a two-layer MLP over 5,000 buckets instead.
  - id: classes
    role: outputHead
    title: Class scores
    details:
    - up to 160 classes
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
tags: []
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


