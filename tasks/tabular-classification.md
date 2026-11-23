---
id: task/tabular-classification
type: task
slug: tabular-classification
schema_version: 3.1
title: Tabular Classification
group: task-group/structured-data
order: 1
summary: Predict a category for each row of a table from the values in its columns.
hf_pipeline_tags:
- tabular-classification
inputs:
- tabular
outputs:
- label
tags:
- tabular
- classification
- structured-data
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/tabular-classification
  title: Hugging Face — Tabular Classification task
  trust: 0.9
---

## What It Is

A tabular classification model reads one row of a table and **assigns it to one of a fixed set of classes**.

- **Mixed columns:** inputs combine numbers, categories and dates, unlike text or images.
- **Gradient-boosted trees remain the baseline:** on most tables they match or beat neural networks.
- **Pretrained tabular models:** newer networks predict on a small table without any training on it.

## When To Use

- **Use it** for yes-or-no and multi-class decisions such as fraud, churn or defect type.
- **Use tabular regression instead** when the target is a number, not a category.
- **Use time-series forecasting instead** when rows are ordered in time and you predict future values.

## How To Choose

- **Table size:** pretrained models suit small tables; trees scale to millions of rows.
- **Column types:** how categories and missing values are handled without manual preparation.
- **Calibration:** whether predicted probabilities can be trusted, not only the top class.
- **Explainability:** how easily a prediction can be traced back to the columns behind it.
