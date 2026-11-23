---
id: model/granite-timeseries-ttm-r1
type: model
slug: granite-timeseries-ttm-r1
schema_version: 1.1
title: granite-timeseries-ttm-r1
organization: organization/ibm-granite
release_date: '2024-04-05'
release_year: 2024
release_month: 4
license: license/apache-2-0
model_category: structured-data
tasks:
- task/time-series-forecasting
parameters_active_b: 0.0
total_parameters: 805280
summary: Granite-TimeSeries-TTM-R1 is a time-series forecasting model from IBM Research. A forecasting model reads the recent history of a measurement, such as a sensor or a sales figure, and predicts its next values. The card describes the TinyTimeMixer (TTM) family as compact pre-trained models with less than 1 million parameters, supporting point forecasts at minutely to hourly resolutions.
intended_use: The card presents the models for multivariate time-series forecasting (several related series at once), in zero-shot use or after fine-tuning on a small share of the data. It says the current open-source version supports point forecasting from minutely to hourly resolutions, for example 10 minutes, 15 minutes or 1 hour.
what_sets_it_apart: 'The card calls these focused pre-trained models: instead of one large model for every case, there is a small model for each forecasting setting, fixed by the context length (how much history it reads) and the forecast length (how far ahead it predicts). The authors say this keeps the models small and fast. R1 was pre-trained on 250M public training samples, and the card points to the later TTM-R2 trained on about 700M samples, advising users to try both.'
running_it: The card says each pre-trained model sits on its own branch of the repository, for example 512-96 (512 past points in, up to 96 ahead) on main and 1024-96-v1 on its own branch, and points to a getting-started notebook. It also says zero-shot, fine-tuning and inference can run on a single GPU machine or a laptop.
fingerprint:
  num_layers: 2
  hidden_size: 192
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: time-series-forecasting
  library: granite-tsfm
hub_signals:
  downloads_30d: 12648
  likes: 332
  last_modified: '2025-01-30'
  observed_at: '2026-11-21'
  source: https://huggingface.co/ibm-granite/granite-timeseries-ttm-r1
aliases:
- ibm-granite/granite-timeseries-ttm-r1
lineage:
  parents: []
  children: []
quotes:
  summary: TinyTimeMixers (TTMs) are compact pre-trained models for Multivariate Time-Series Forecasting, open-sourced by IBM Research.
  intended_use: The current open-source version supports point forecasting use-cases specifically ranging from minutely to hourly resolutions
  what_sets_it_apart: TTM falls under the category of “focused pre-trained models”
  running_it: Each pre-trained model will be released in a different branch name in this model card.
tags:
- time-series-forecasting
- tinytimemixer
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/ibm-granite/granite-timeseries-ttm-r1
  title: ibm-granite/granite-timeseries-ttm-r1 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/ibm-granite/granite-timeseries-ttm-r1/blob/main/config.json
  title: ibm-granite/granite-timeseries-ttm-r1 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2401.03955
  title: arXiv:2401.03955
  trust: 0.95
---

## Summary

Granite-TimeSeries-TTM-R1 is a time-series forecasting model from IBM Research. A forecasting model reads the recent history of a measurement, such as a sensor or a sales figure, and predicts its next values. The card describes the TinyTimeMixer (TTM) family as compact pre-trained models with less than 1 million parameters, supporting point forecasts at minutely to hourly resolutions.

## Intended Use

The card presents the models for multivariate time-series forecasting (several related series at once), in zero-shot use or after fine-tuning on a small share of the data. It says the current open-source version supports point forecasting from minutely to hourly resolutions, for example 10 minutes, 15 minutes or 1 hour.

## What Sets It Apart

The card calls these focused pre-trained models: instead of one large model for every case, there is a small model for each forecasting setting, fixed by the context length (how much history it reads) and the forecast length (how far ahead it predicts). The authors say this keeps the models small and fast. R1 was pre-trained on 250M public training samples, and the card points to the later TTM-R2 trained on about 700M samples, advising users to try both.

## Running It

The card says each pre-trained model sits on its own branch of the repository, for example 512-96 (512 past points in, up to 96 ahead) on main and 1024-96-v1 on its own branch, and points to a getting-started notebook. It also says zero-shot, fine-tuning and inference can run on a single GPU machine or a laptop.
