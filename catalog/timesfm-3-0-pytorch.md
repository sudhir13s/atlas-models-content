---
id: model/timesfm-3-0-pytorch
type: model
slug: timesfm-3-0-pytorch
schema_version: 1.1
title: timesfm-3.0-pytorch
organization: organization/google-deepmind
release_date: '2026-08-24'
release_year: 2026
release_month: 8
license_note: timesfm-non-commercial-license-v1.0
model_category: structured-data
tasks:
- task/time-series-forecasting
parameters_active_b: 0.33
total_parameters: 330710976
summary: 'TimesFM 3.0 is a time-series foundation model from Google Research, used for forecasting: predicting the future values of a series of numbers measured over time. This repository holds the official PyTorch weights and configuration. The weights are released under the TimesFM Non-Commercial License v1.0.'
what_sets_it_apart: The card lists a Stacked Mixing Transformer with Variate Attention and CPM Iterative RevIN, with 20 transformer layers, a model dimension of 1280 and 16 heads. It works on patches of 32 context points and 64 forecast points. Pretraining used GiftEvalPretrain (without the datasets that overlap with fev-bench), Wikipedia Pageviews up to November 2023, Google Trends top queries up to the end of 2022, and synthetic and augmented data.
pipeline:
  source: https://github.com/google-research/timesfm/blob/master/src/timesfm3/torch/model.py
  summary: The target series and any covariates are cut into patches of 32 points, normalised with running statistics and embedded by a residual block. Each of 20 layers attends back along time within a series, then across the series at the same patch, and a linear head writes the next 64 points at nine quantiles for each patch.
  stages:
  - id: series
    role: input
    title: Series + covariates
    details:
    - target and covariate variates
    - up to 32 variates
  - id: patches
    role: tokenizer
    title: Patching
    details:
    - 32 points per patch
  - id: revin
    role: normalisation
    title: Running normalisation
    details:
    - RevIN · undone at the output
    note: Reversible instance normalisation (RevIN). Each patch is scaled with the running mean and standard deviation of the points seen so far, and the forecast is scaled back with the same statistics.
  - id: resblock
    role: embedding
    title: Input residual block
    details:
    - patch + next 64 covariate points
    - masks · width 1280
    note: Each patch's values, the covariate values for the 64 points that follow (when known) and the masks for missing points are joined and mapped by a residual block to one vector.
  - id: sequence-attention
    role: attention
    title: Sequence attention
    details:
    - 16 heads · causal · RoPE
    frame: Mixing layer
    repeat: 20
    note: Within one series, each patch attends to itself and earlier patches only, with rotary position embeddings (RoPE).
  - id: variate-attention
    role: attention
    title: Variate attention
    details:
    - across series at the same patch
    frame: Mixing layer
    note: At each patch position, the target and covariate series attend to each other, with no causal mask.
  - id: feed-forward
    role: feedForward
    title: Feed-forward
    details:
    - width 1280 · ReLU
    frame: Mixing layer
  - id: head
    role: outputHead
    title: Output head
    details:
    - linear · 64 points × 9 quantiles
    note: Every patch predicts the 64 points after it, at the quantiles 0.1 to 0.9. Over the forecast region the normalisation statistics are refined step by step from the model's own median estimates before scaling back.
  - id: forecast
    role: outputHead
    title: Quantile forecast
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: time-series-forecasting
hub_signals:
  downloads_30d: 389414
  likes: 1035
  last_modified: '2026-09-02'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/timesfm-3.0-pytorch
aliases:
- google/timesfm-3.0-pytorch
lineage:
  parents: []
  children: []
quotes:
  summary: TimesFM (Time Series Foundation Model) is a pretrained time-series foundation model developed by Google Research for time-series forecasting.
  what_sets_it_apart: Stacked Mixing Transformer with Variate Attention and CPM Iterative RevIN.
tags:
- time-series-forecasting
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/timesfm-3.0-pytorch
  title: google/timesfm-3.0-pytorch — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/timesfm-3.0-pytorch/blob/main/config.json
  title: google/timesfm-3.0-pytorch — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2310.10688
  title: arXiv:2310.10688
  trust: 0.95
- url: https://github.com/google-research/timesfm/blob/master/src/timesfm3/torch/model.py
  title: 'timesfm/src/timesfm3/torch/model.py: TimesFM 3 PyTorch model code (Google Research)'
  trust: 0.9
- url: https://github.com/google-research/timesfm/blob/master/src/timesfm3/torch/transformer.py
  title: 'timesfm/src/timesfm3/torch/transformer.py: TimesFM 3 mixing transformer (Google Research)'
  trust: 0.9
---

## Summary

TimesFM 3.0 is a time-series foundation model from Google Research, used for forecasting: predicting the future values of a series of numbers measured over time. This repository holds the official PyTorch weights and configuration. The weights are released under the TimesFM Non-Commercial License v1.0.

## What Sets It Apart

The card lists a Stacked Mixing Transformer with Variate Attention and CPM Iterative RevIN, with 20 transformer layers, a model dimension of 1280 and 16 heads. It works on patches of 32 context points and 64 forecast points. Pretraining used GiftEvalPretrain (without the datasets that overlap with fev-bench), Wikipedia Pageviews up to November 2023, Google Trends top queries up to the end of 2022, and synthetic and augmented data.
