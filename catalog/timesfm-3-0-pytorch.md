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
---

## Summary

TimesFM 3.0 is a time-series foundation model from Google Research, used for forecasting: predicting the future values of a series of numbers measured over time. This repository holds the official PyTorch weights and configuration. The weights are released under the TimesFM Non-Commercial License v1.0.

## What Sets It Apart

The card lists a Stacked Mixing Transformer with Variate Attention and CPM Iterative RevIN, with 20 transformer layers, a model dimension of 1280 and 16 heads. It works on patches of 32 context points and 64 forecast points. Pretraining used GiftEvalPretrain (without the datasets that overlap with fev-bench), Wikipedia Pageviews up to November 2023, Google Trends top queries up to the end of 2022, and synthetic and augmented data.
