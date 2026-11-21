---
id: model/donut-base-finetuned-docvqa
type: model
slug: donut-base-finetuned-docvqa
schema_version: 1.1
title: donut-base-finetuned-docvqa
organization: organization/naver-clova-ix
release_date: '2022-07-19'
release_year: 2022
release_month: 7
license: license/mit
model_category: documents
tasks:
- task/document-question-answering
context_window_tokens: 128
fingerprint:
  num_layers: 12
  hidden_size: 1024
  context_window_tokens: 128
  vocabulary_size: 57532
  activation_function: gelu
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: document-question-answering
  library: transformers
hub_signals:
  downloads_30d: 7392
  likes: 285
  last_modified: '2024-03-09'
  observed_at: '2026-11-21'
  source: https://huggingface.co/naver-clova-ix/donut-base-finetuned-docvqa
aliases:
- naver-clova-ix/donut-base-finetuned-docvqa
lineage:
  parents: []
  children: []
tags:
- document-question-answering
- vision-encoder-decoder
- dense
updated: '2026-11-21'
sources:
- url: https://huggingface.co/naver-clova-ix/donut-base-finetuned-docvqa
  title: naver-clova-ix/donut-base-finetuned-docvqa — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/naver-clova-ix/donut-base-finetuned-docvqa/blob/main/config.json
  title: naver-clova-ix/donut-base-finetuned-docvqa — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2111.15664
  title: arXiv:2111.15664
  trust: 0.95
---


