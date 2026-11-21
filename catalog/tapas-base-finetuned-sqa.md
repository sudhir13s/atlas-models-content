---
id: model/tapas-base-finetuned-sqa
type: model
slug: tapas-base-finetuned-sqa
schema_version: 1.1
title: tapas-base-finetuned-sqa
organization: organization/google-deepmind
license: license/apache-2-0
model_category: documents
tasks:
- task/table-question-answering
context_window_tokens: 1024
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 1024
  vocabulary_size: 30522
  activation_function: gelu
  normalization: LayerNorm
filters:
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: table-question-answering
  library: transformers
hub_signals:
  downloads_30d: 1850
  likes: 14
  last_modified: '2021-11-29'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/tapas-base-finetuned-sqa
aliases:
- google/tapas-base-finetuned-sqa
lineage:
  parents: []
  children: []
tags:
- table-question-answering
- tapas
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/google/tapas-base-finetuned-sqa
  title: google/tapas-base-finetuned-sqa — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/tapas-base-finetuned-sqa/blob/main/config.json
  title: google/tapas-base-finetuned-sqa — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2004.02349
  title: arXiv:2004.02349
  trust: 0.95
- url: https://arxiv.org/abs/2010.00571
  title: arXiv:2010.00571
  trust: 0.95
---


