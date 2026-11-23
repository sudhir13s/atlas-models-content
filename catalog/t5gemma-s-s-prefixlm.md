---
id: model/t5gemma-s-s-prefixlm
type: model
slug: t5gemma-s-s-prefixlm
schema_version: 3.0
title: t5gemma-s-s-prefixlm
organization: organization/google-deepmind
release_date: '2025-06-19'
license: license/gemma
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
total_parameters: 312517632
summary: T5Gemma is a family of encoder-decoder research models from Google. An encoder-decoder model reads the whole input with one network and writes the output with a second one, which suits tasks such as translation or summarization. This checkpoint is the Small-Small variant pre-trained with the PrefixLM objective, as its name indicates, and the card says the family is text-to-text and available in English with open weights.
intended_use: The card says T5Gemma models are well suited to generative tasks such as question answering, summarization and reasoning, while their encoders can be used for discriminative tasks such as classification and understanding. It adds that the models are small enough to run on laptops, desktops or your own cloud.
what_sets_it_apart: The card says T5Gemma models are made by adapting pretrained decoder-only models into encoder-decoder models, so that they inherit the decoder-only models' abilities while offering a more favorable quality-efficiency trade-off. It says the encoder and decoder can be paired at different sizes, for example a 9B encoder with a 2B decoder.
filters:
  size_category: tiny
  is_gated: true
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 47363
  likes: 5
  last_modified: '2025-07-09'
  observed_at: '2026-11-20'
  source: https://huggingface.co/google/t5gemma-s-s-prefixlm
aliases:
- google/t5gemma-s-s-prefixlm
lineage:
  parents: []
  children: []
quotes:
  summary: T5Gemma is a family of lightweight yet powerful encoder-decoder research models from Google.
  intended_use: T5Gemma models are well-suited for a variety of generative tasks, including question answering, summarization, and reasoning.
  what_sets_it_apart: These models are created by adapting pretrained decoder-only models into a encoder-decoder.
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/t5gemma-s-s-prefixlm
  title: google/t5gemma-s-s-prefixlm — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2504.06225
  title: arXiv:2504.06225
  trust: 0.95
- url: https://arxiv.org/abs/2009.03300
  title: arXiv:2009.03300
  trust: 0.95
- url: https://arxiv.org/abs/1905.07830
  title: arXiv:1905.07830
  trust: 0.95
- url: https://arxiv.org/abs/1911.11641
  title: arXiv:1911.11641
  trust: 0.95
- url: https://arxiv.org/abs/1905.10044
  title: arXiv:1905.10044
  trust: 0.95
- url: https://arxiv.org/abs/1907.10641
  title: arXiv:1907.10641
  trust: 0.95
- url: https://arxiv.org/abs/1911.01547
  title: arXiv:1911.01547
  trust: 0.95
- url: https://arxiv.org/abs/1705.03551
  title: arXiv:1705.03551
  trust: 0.95
- url: https://arxiv.org/abs/2107.03374
  title: arXiv:2107.03374
  trust: 0.95
- url: https://arxiv.org/abs/2108.07732
  title: arXiv:2108.07732
  trust: 0.95
- url: https://arxiv.org/abs/2110.14168
  title: arXiv:2110.14168
  trust: 0.95
- url: https://arxiv.org/abs/2103.03874
  title: arXiv:2103.03874
  trust: 0.95
- url: https://arxiv.org/abs/2304.06364
  title: arXiv:2304.06364
  trust: 0.95
- url: https://arxiv.org/abs/2206.04615
  title: arXiv:2206.04615
  trust: 0.95
---


