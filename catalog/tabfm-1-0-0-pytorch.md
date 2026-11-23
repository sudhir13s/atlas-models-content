---
id: model/tabfm-1-0-0-pytorch
type: model
slug: tabfm-1-0-0-pytorch
schema_version: 3.0
title: tabfm-1.0.0-pytorch
organization: organization/google-deepmind
release_date: '2026-06-29'
license_note: tabfm-non-commercial-v1.0
model_category: structured-data
model_kind: tabular-time-series
tasks:
- task/tabular-classification
summary: 'TabFM 1.0.0 is a tabular foundation model from Google Research. Tabular data is rows and columns, like a spreadsheet, and the model does classification and regression on tables with numerical and categorical columns. The card says it needs no fine-tuning or hyperparameter search: the training rows are passed in as context and predictions come out in a single forward pass. This repository holds the PyTorch weights.'
intended_use: The card lists tabular data with numerical or categorical columns, binary and multiclass classification with up to 10 classes, regression on continuous targets, and zero-shot use with no dataset-specific training. It lists as not intended images, audio, video or raw text, more than 10 output classes, tasks needing task-specific fine-tuning, non-tabular structured data, and commercial use, which its license does not permit.
what_sets_it_apart: 'The card describes alternating column and row attention: a column step that embeds each cell, a row step that compresses each row into one vector, and a 24-block causal transformer that treats the training rows as context and outputs predictions for test rows. It says the model was trained on hundreds of millions of synthetic datasets generated from structural causal models, chosen because of the scarcity of diverse open tabular datasets and to avoid privacy and licensing concerns.'
limitations: The card gives a hard limit of 10 classes, says memory use grows with the number of training rows because all rows are passed as context, and says it is optimized for tables of up to 500 features. It states that performance is not guaranteed to match fine-tuned models on every dataset, that behavior on real-world domains and minority groups is not fully characterised, and that it is not an officially supported Google product.
pipeline:
  source: https://github.com/google-research/tabfm/blob/main/tabfm/src/pytorch/model.py
  summary: Every table cell becomes a vector. Two rounds of column attention and row attention mix the cells and leave each row summarised by eight learned summary tokens (CLS tokens). A 24-block transformer then reads the row vectors with the training labels added, and a small head gives the class scores for each test row.
  stages:
  - id: table
    role: input
    title: Table
    details:
    - training rows with labels
    - test rows
  - id: cell-embedding
    role: embedding
    title: Cell embedding
    details:
    - Fourier features · 32 frequencies
    - width 256 · + label on training rows
    note: Each value is expanded into sine and cosine features and projected by a linear layer per group of three features. On training rows an embedding of the row's label is added to every cell.
  - id: column-attention
    role: attention
    title: Column attention
    details:
    - 3 blocks · 4 heads
    - 256 induced points
    frame: Column then row
    repeat: 2
    note: A Set Transformer runs down each column. A set of 256 learned points first reads the training rows of the column, then every row reads those points, so each cell learns how its value sits among the others.
  - id: row-attention
    role: attention
    title: Row attention
    details:
    - 3 blocks · 8 heads · RoPE
    frame: Column then row
    note: Attention across the cells of one row, with rotary position embedding (RoPE). Before the first round, eight learned CLS tokens are placed at the start of every row; after the second round only they are kept.
  - id: row-vector
    role: projector
    title: Row vector
    details:
    - 8 CLS tokens × 256 = width 2048
    note: The eight summary tokens of each row are joined into one vector.
  - id: add-labels
    role: embedding
    title: Add label encoding
    details:
    - training rows only
    note: A one-hot label passed through a linear layer is added to the vector of each training row; test rows get nothing added.
  - id: icl-block
    role: attention
    title: Attention + feed-forward
    details:
    - 8 heads · width 2048 · SwiGLU
    - every row reads the training rows
    frame: ICL transformer
    repeat: 24
    note: In-context learning (ICL). The attention mask lets every row look only at the training rows, so test rows take what they need from the labelled examples. There is no position encoding in this part.
  - id: norm
    role: normalisation
    title: Final RMSNorm
  - id: head
    role: outputHead
    title: Output head
    details:
    - MLP · scores for up to 10 classes
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
- tabfm
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/tabfm-1.0.0-pytorch
  title: google/tabfm-1.0.0-pytorch — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/tabfm-1.0.0-pytorch/blob/main/config.json
  title: google/tabfm-1.0.0-pytorch — config.json
  trust: 0.9
- url: https://github.com/google-research/tabfm/blob/main/tabfm/src/pytorch/model.py
  title: 'tabfm/src/pytorch/model.py: TabFM PyTorch model code (Google Research)'
  trust: 0.9
- url: https://huggingface.co/google/tabfm-1.0.0-pytorch/blob/main/classification/config.json
  title: google/tabfm-1.0.0-pytorch — classification/config.json
  trust: 0.9
---


