---
id: model/mdeberta-v3-base-xnli-multilingual-nli-2mil7
type: model
slug: mdeberta-v3-base-xnli-multilingual-nli-2mil7
schema_version: 1.1
title: mDeBERTa-v3-base-xnli-multilingual-nli-2mil7
organization: organization/moritzlaurer
release_date: '2022-08-22'
release_year: 2022
release_month: 8
license: license/mit
model_category: language
tasks:
- task/zero-shot-classification
parameters_active_b: 0.28
total_parameters: 278812163
context_window_tokens: 512
summary: 'This is a third-party fine-tune, published under the MoritzLaurer account on Hugging Face, of Microsoft''s mDeBERTa-v3-base. It performs natural language inference (NLI): given a premise and a hypothesis, it says whether the premise entails the hypothesis, is neutral to it, or contradicts it. The card says it covers 100 languages and can also do multilingual zero-shot classification, which sorts a text into labels you supply without task-specific training.'
intended_use: The card presents it for natural language inference and zero-shot classification, with examples that check an English hypothesis against a German text. Its training mix pairs English hypotheses with premises in other languages so that users can write a hypothesis in English for a text in another language.
what_sets_it_apart: The card says the underlying mDeBERTa-v3-base was pre-trained by Microsoft on 100 languages and then fine-tuned on the XNLI dataset and on a 26-language NLI dataset of more than 2.7 million pairs, with English added for 27 languages in all. That dataset was made by machine-translating English NLI datasets (MultiNLI, Fever-NLI, ANLI, LingNLI and WANLI). Bulgarian, Greek and Thai were kept out of NLI training so that cross-lingual transfer could be tested.
limitations: The card says the multilingual dataset was machine-translated, which lowers data quality for a complex task like NLI. It adds that performance on languages seen only in pre-training, not in NLI fine-tuning, is most likely lower. It points to the DeBERTa-V3 paper and NLI literature for potential biases.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 512
  vocabulary_size: 251000
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: post
  stack: encoder
  embedding_norm: true
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: zero-shot-classification
  library: transformers
hub_signals:
  downloads_30d: 758679
  likes: 412
  last_modified: '2024-04-11'
  observed_at: '2026-11-21'
  source: https://huggingface.co/MoritzLaurer/mDeBERTa-v3-base-xnli-multilingual-nli-2mil7
aliases:
- MoritzLaurer/mDeBERTa-v3-base-xnli-multilingual-nli-2mil7
lineage:
  parents: []
  children: []
quotes:
  summary: This multilingual model can perform natural language inference (NLI) on 100 languages and is therefore also suitable for multilingual zero-shot classification.
  intended_use: This mix of languages in the text pairs should enable users to formulate a hypothesis in English for a target text in another language.
  what_sets_it_apart: was created using the latest open-source machine translation models
  limitations: which reduces the quality of the data for a complex task like NLI
tags:
- llm
- zero-shot-classification
- deberta-v2
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/MoritzLaurer/mDeBERTa-v3-base-xnli-multilingual-nli-2mil7
  title: MoritzLaurer/mDeBERTa-v3-base-xnli-multilingual-nli-2mil7 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/MoritzLaurer/mDeBERTa-v3-base-xnli-multilingual-nli-2mil7/blob/main/config.json
  title: MoritzLaurer/mDeBERTa-v3-base-xnli-multilingual-nli-2mil7 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2111.09543
  title: arXiv:2111.09543
  trust: 0.95
- url: https://arxiv.org/abs/2104.07179
  title: arXiv:2104.07179
  trust: 0.95
- url: https://arxiv.org/abs/1809.05053
  title: arXiv:1809.05053
  trust: 0.95
- url: https://arxiv.org/abs/1911.02116
  title: arXiv:1911.02116
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/deberta_v2/modeling_deberta_v2.py
  title: 'transformers/models/deberta_v2/modeling_deberta_v2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

This is a third-party fine-tune, published under the MoritzLaurer account on Hugging Face, of Microsoft's mDeBERTa-v3-base. It performs natural language inference (NLI): given a premise and a hypothesis, it says whether the premise entails the hypothesis, is neutral to it, or contradicts it. The card says it covers 100 languages and can also do multilingual zero-shot classification, which sorts a text into labels you supply without task-specific training.

## Intended Use

The card presents it for natural language inference and zero-shot classification, with examples that check an English hypothesis against a German text. Its training mix pairs English hypotheses with premises in other languages so that users can write a hypothesis in English for a text in another language.

## What Sets It Apart

The card says the underlying mDeBERTa-v3-base was pre-trained by Microsoft on 100 languages and then fine-tuned on the XNLI dataset and on a 26-language NLI dataset of more than 2.7 million pairs, with English added for 27 languages in all. That dataset was made by machine-translating English NLI datasets (MultiNLI, Fever-NLI, ANLI, LingNLI and WANLI). Bulgarian, Greek and Thai were kept out of NLI training so that cross-lingual transfer could be tested.

## Limitations

The card says the multilingual dataset was machine-translated, which lowers data quality for a complex task like NLI. It adds that performance on languages seen only in pre-training, not in NLI fine-tuning, is most likely lower. It points to the DeBERTa-V3 paper and NLI literature for potential biases.
