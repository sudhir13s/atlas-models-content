---
id: model/mdeberta-v3-base-mnli-xnli
type: model
slug: mdeberta-v3-base-mnli-xnli
schema_version: 1.1
title: mDeBERTa-v3-base-mnli-xnli
organization: organization/moritzlaurer
license: license/mit
model_category: language
tasks:
- task/zero-shot-classification
parameters_active_b: 0.28
total_parameters: 278812163
context_window_tokens: 512
summary: 'mDeBERTa-v3-base-mnli-xnli is a multilingual model for natural language inference (NLI): given a premise and a hypothesis, it says whether the premise entails the hypothesis, contradicts it, or neither. That also makes it usable for zero-shot classification, sorting text into labels it was never trained on. It is a fine-tune, published under the MoritzLaurer account, of Microsoft''s mDeBERTa base model, which Microsoft pre-trained on the CC100 multilingual dataset, trained here on the XNLI and MNLI datasets. The card states it handles NLI in 100 languages.'
intended_use: The card presents it for NLI and for multilingual zero-shot classification. It notes that multilingual NLI models can classify texts in a language without NLI training data in that language, called cross-lingual transfer, so it can also work in the other languages mDeBERTa was pre-trained on, though performance there is most likely lower than for the 15 languages in XNLI.
what_sets_it_apart: 'The card says the model was trained only on the professional translations in the XNLI development set and on the original English MNLI training set, not on the machine-translated XNLI training data. It gives three reasons: it avoids overfitting to the 15 languages, avoids forgetting the other 85 languages mDeBERTa was pre-trained on, and reduces training cost.'
running_it: The card says mDeBERTa currently does not support FP16, a half-precision number format, and that older versions of Transformers can have tokenizer issues, so version 4.13 or higher may be needed.
limitations: The card gives no bias analysis of its own and tells readers to consult the original DeBERTa-V3 paper and the literature on NLI datasets for potential biases.
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
  downloads_30d: 199157
  likes: 324
  last_modified: '2024-01-08'
  observed_at: '2026-11-21'
  source: https://huggingface.co/MoritzLaurer/mDeBERTa-v3-base-mnli-xnli
aliases:
- MoritzLaurer/mDeBERTa-v3-base-mnli-xnli
lineage:
  parents: []
  children: []
quotes:
  summary: This multilingual model can perform natural language inference (NLI) on 100 languages
  intended_use: multilingual NLI models are capable of classifying NLI texts without receiving NLI training data in the specific language (cross-lingual transfer)
  what_sets_it_apart: this model was only trained on the professional translations from the XNLI development set and the original English MNLI training set
  running_it: mDeBERTa currently does not support FP16
  limitations: Please consult the original DeBERTa-V3 paper and literature on different NLI datasets for potential biases.
tags:
- llm
- zero-shot-classification
- deberta-v2
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/MoritzLaurer/mDeBERTa-v3-base-mnli-xnli
  title: MoritzLaurer/mDeBERTa-v3-base-mnli-xnli — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/MoritzLaurer/mDeBERTa-v3-base-mnli-xnli/blob/main/config.json
  title: MoritzLaurer/mDeBERTa-v3-base-mnli-xnli — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2111.09543
  title: arXiv:2111.09543
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

mDeBERTa-v3-base-mnli-xnli is a multilingual model for natural language inference (NLI): given a premise and a hypothesis, it says whether the premise entails the hypothesis, contradicts it, or neither. That also makes it usable for zero-shot classification, sorting text into labels it was never trained on. It is a fine-tune, published under the MoritzLaurer account, of Microsoft's mDeBERTa base model, which Microsoft pre-trained on the CC100 multilingual dataset, trained here on the XNLI and MNLI datasets. The card states it handles NLI in 100 languages.

## Intended Use

The card presents it for NLI and for multilingual zero-shot classification. It notes that multilingual NLI models can classify texts in a language without NLI training data in that language, called cross-lingual transfer, so it can also work in the other languages mDeBERTa was pre-trained on, though performance there is most likely lower than for the 15 languages in XNLI.

## What Sets It Apart

The card says the model was trained only on the professional translations in the XNLI development set and on the original English MNLI training set, not on the machine-translated XNLI training data. It gives three reasons: it avoids overfitting to the 15 languages, avoids forgetting the other 85 languages mDeBERTa was pre-trained on, and reduces training cost.

## Running It

The card says mDeBERTa currently does not support FP16, a half-precision number format, and that older versions of Transformers can have tokenizer issues, so version 4.13 or higher may be needed.

## Limitations

The card gives no bias analysis of its own and tells readers to consult the original DeBERTa-V3 paper and the literature on NLI datasets for potential biases.
