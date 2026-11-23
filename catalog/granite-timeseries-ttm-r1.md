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
pipeline:
  source: https://arxiv.org/abs/2401.03955
  summary: Each series is scaled and cut into eight patches of 64 steps. A small backbone of mixer blocks (multilayer perceptrons, no self-attention) works at three patch sizes in turn, a lighter decoder refines the result, and a linear head writes the next 96 values, which are scaled back.
  stages:
  - id: history
    role: input
    title: Past values
    details:
    - 512 steps per series
    note: The main branch holds the 512-in, 96-out model; other context and forecast lengths are separate models on other branches. In pre-training each series is handled on its own.
  - id: scaling
    role: normalisation
    title: Standard scaling
    details:
    - per series · undone at the end
  - id: patches
    role: tokenizer
    title: Patching
    details:
    - 8 patches of 64 steps
  - id: patch-embedding
    role: embedding
    title: Patch embedding
    details:
    - linear · width 192
  - id: split
    role: projector
    title: Patch partition
    details:
    - 4×, 2×, then 1× more patches
    frame: Backbone level
    repeat: 3
    note: Adaptive patching. At the first level each patch vector is split into four narrower ones, at the second into two, at the third it is left whole, so the levels see the series at different granularities.
  - id: mixer
    role: feedForward
    title: TSMixer block
    details:
    - 2 per level
    - mixes across and within patches
    frame: Backbone level
    note: Each block mixes information across patches, then across the features of each patch, using multilayer perceptrons with a gated attention step instead of self-attention.
  - id: merge
    role: projector
    title: Patch merging
    details:
    - back to 8 patches · width 192
    frame: Backbone level
  - id: decoder
    role: feedForward
    title: TTM decoder
    details:
    - 2 mixer layers · width 128
    note: Built like the backbone but much smaller. When fine-tuning, its channel mixing can be switched on to model links between series, and an optional exogenous mixer can use known future inputs.
  - id: head
    role: outputHead
    title: Forecast head
    details:
    - linear · 96 values ahead
    note: Produces a point forecast for each series, which is then returned to the original scale.
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
- url: https://github.com/ibm-granite/granite-tsfm/blob/main/tsfm_public/models/tinytimemixer/modeling_tinytimemixer.py
  title: 'granite-tsfm/tsfm_public/models/tinytimemixer/modeling_tinytimemixer.py: TinyTimeMixer model code (IBM)'
  trust: 0.9
---

## Summary

Granite-TimeSeries-TTM-R1 is a time-series forecasting model from IBM Research. A forecasting model reads the recent history of a measurement, such as a sensor or a sales figure, and predicts its next values. The card describes the TinyTimeMixer (TTM) family as compact pre-trained models with less than 1 million parameters, supporting point forecasts at minutely to hourly resolutions.

## Intended Use

The card presents the models for multivariate time-series forecasting (several related series at once), in zero-shot use or after fine-tuning on a small share of the data. It says the current open-source version supports point forecasting from minutely to hourly resolutions, for example 10 minutes, 15 minutes or 1 hour.

## What Sets It Apart

The card calls these focused pre-trained models: instead of one large model for every case, there is a small model for each forecasting setting, fixed by the context length (how much history it reads) and the forecast length (how far ahead it predicts). The authors say this keeps the models small and fast. R1 was pre-trained on 250M public training samples, and the card points to the later TTM-R2 trained on about 700M samples, advising users to try both.

## Running It

The card says each pre-trained model sits on its own branch of the repository, for example 512-96 (512 past points in, up to 96 ahead) on main and 1024-96-v1 on its own branch, and points to a getting-started notebook. It also says zero-shot, fine-tuning and inference can run on a single GPU machine or a laptop.
