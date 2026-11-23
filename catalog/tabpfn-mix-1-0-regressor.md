---
id: model/tabpfn-mix-1-0-regressor
type: model
slug: tabpfn-mix-1-0-regressor
schema_version: 3.0
title: tabpfn-mix-1.0-regressor
organization: organization/autogluon
release_date: '2024-11-26'
license: license/apache-2-0
model_category: structured-data
model_kind: tabular-time-series
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
pipeline:
  source: https://github.com/autogluon/autogluon/blob/master/tabular/src/autogluon/tabular/models/tabpfnmix/_internal/models/foundation/foundation_transformer.py
  summary: Each table row becomes one vector, built from its features and, for the training rows, its known target. In 12 transformer layers every row attends to the training rows, so the rows to predict read the examples in context. A small head turns each predicted row's vector into a number.
  stages:
  - id: table
    role: input
    title: Table
    details:
    - training rows with targets
    - rows to predict
    note: The training rows are given as context in the same forward pass instead of being used to retrain the model.
  - id: feature-embedding
    role: embedding
    title: Row embedding
    details:
    - linear · 100 feature slots → 512
    note: A linear layer maps all the feature values of a row to one vector of width 512.
  - id: target-embedding
    role: embedding
    title: Add target embedding
    details:
    - known target → vector · else zero
    note: For a training row, a linear layer turns its target value into a vector that is added to the row's vector. The rows to predict get nothing added.
  - id: attention
    role: attention
    title: Attention to training rows
    details:
    - 4 heads · width 512
    frame: Layer
    repeat: 12
    note: Every row, training row or row to predict, uses the training rows as keys and values. The rows to predict never attend to each other.
  - id: feed-forward
    role: feedForward
    title: Feed-forward
    details:
    - width 2048 · GELU
    frame: Layer
  - id: head
    role: outputHead
    title: Output head
    details:
    - two linear layers · one number
    note: A linear layer of width 2048 with GELU and a second linear layer give one predicted value for each row to predict.
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
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
tags: []
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
- url: https://github.com/autogluon/autogluon/blob/master/tabular/src/autogluon/tabular/models/tabpfnmix/_internal/models/foundation/foundation_transformer.py
  title: 'autogluon/tabular/models/tabpfnmix/_internal/models/foundation/foundation_transformer.py: TabPFNMix model code (AutoGluon)'
  trust: 0.9
---


