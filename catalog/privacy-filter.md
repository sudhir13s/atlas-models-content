---
id: model/privacy-filter
type: model
slug: privacy-filter
schema_version: 1.1
title: privacy-filter
organization: organization/openai
release_date: '2026-04-17'
release_year: 2026
release_month: 4
license: license/apache-2-0
model_category: language
tasks:
- task/token-classification
total_parameters: 1399486865
context_window_tokens: 128000
summary: 'OpenAI Privacy Filter is a token-classification model from OpenAI: it reads text and labels each token, a word or word piece, so that personally identifiable information (PII) can be found and masked. The card lists 8 categories, including names, emails, phone numbers, addresses and secrets. It gives 1.5B parameters in total with 50M active, a 128,000-token context window, and primarily English as the language.'
intended_use: The card says it is intended for high-throughput data sanitization workflows where teams need a model they can run on-premises that is fast, context-aware and tunable. It states that the tool is a redaction and data minimization aid, not an anonymization, compliance or safety guarantee, and asks for extra caution in medical, legal, financial, human resources, education and government settings.
what_sets_it_apart: OpenAI says it was pretrained like a language model, with an architecture similar to gpt-oss but smaller, then converted into a bidirectional token classifier and post-trained with a supervised classification loss. Instead of writing text one token at a time, it labels the whole input in a single pass and then decodes coherent spans with a constrained Viterbi procedure, which picks the best consistent path of labels. Its feed-forward blocks are sparse mixture-of-experts layers with 128 experts.
running_it: The card says precision and recall can be traded off at runtime, and detected span lengths adjusted, through preset operating points. It shows use through the Transformers token-classification pipeline and through Transformers.js in a browser with WebGPU.
limitations: The card says the model only finds personal data that matches its trained label set, and that changing the label policy needs further fine-tuning. Performance may drop on non-English text, non-Latin scripts and domains unlike its training data. It lists failure modes such as missing uncommon names, over-redacting public entities, shifted span boundaries and missing novel secret formats, and advises evaluating in your own domain and keeping human review for high-sensitivity work.
fingerprint:
  attention_type: GQA
  num_layers: 8
  attention_heads: 14
  kv_heads: 2
  head_size: 64
  hidden_size: 640
  intermediate_size: 640
  context_window_tokens: 128000
  vocabulary_size: 200064
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: encoder
  positional_encoding: RoPE
  rope_theta: 150000.0
moe_config:
  expert_count: 128
  active_experts: 4
filters:
  size_category: small
  is_quantized: false
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: token-classification
  library: transformers
hub_signals:
  downloads_30d: 114097
  likes: 1841
  last_modified: '2026-04-22'
  observed_at: '2026-11-21'
  source: https://huggingface.co/openai/privacy-filter
aliases:
- openai/privacy-filter
lineage:
  parents: []
  children: []
quotes:
  summary: OpenAI Privacy Filter is a bidirectional token-classification model for personally identifiable information (PII) detection and masking in text.
  intended_use: It is intended for high-throughput data sanitization workflows where teams need a model that they can run on-premises that is fast, context-aware, and tunable.
  what_sets_it_apart: Instead of generating text token-by-token, this model labels an input sequence in a single forward pass, then decodes coherent spans with a constrained Viterbi procedure.
  running_it: 'Runtime control: configure precision/recall tradeoffs and detected span lengths through preset operating points.'
  limitations: Privacy Filter is a redaction and data minimization aid, not an anonymization, compliance, or a safety guarantee.
locked:
- context_window_tokens
- fingerprint.context_window_tokens
tags:
- llm
- token-classification
- openai-privacy-filter
- moe
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/openai/privacy-filter
  title: openai/privacy-filter — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai/privacy-filter/blob/main/config.json
  title: openai/privacy-filter — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/openai_privacy_filter/modeling_openai_privacy_filter.py
  title: 'transformers/models/openai_privacy_filter/modeling_openai_privacy_filter.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

OpenAI Privacy Filter is a token-classification model from OpenAI: it reads text and labels each token, a word or word piece, so that personally identifiable information (PII) can be found and masked. The card lists 8 categories, including names, emails, phone numbers, addresses and secrets. It gives 1.5B parameters in total with 50M active, a 128,000-token context window, and primarily English as the language.

## Intended Use

The card says it is intended for high-throughput data sanitization workflows where teams need a model they can run on-premises that is fast, context-aware and tunable. It states that the tool is a redaction and data minimization aid, not an anonymization, compliance or safety guarantee, and asks for extra caution in medical, legal, financial, human resources, education and government settings.

## What Sets It Apart

OpenAI says it was pretrained like a language model, with an architecture similar to gpt-oss but smaller, then converted into a bidirectional token classifier and post-trained with a supervised classification loss. Instead of writing text one token at a time, it labels the whole input in a single pass and then decodes coherent spans with a constrained Viterbi procedure, which picks the best consistent path of labels. Its feed-forward blocks are sparse mixture-of-experts layers with 128 experts.

## Running It

The card says precision and recall can be traded off at runtime, and detected span lengths adjusted, through preset operating points. It shows use through the Transformers token-classification pipeline and through Transformers.js in a browser with WebGPU.

## Limitations

The card says the model only finds personal data that matches its trained label set, and that changing the label policy needs further fine-tuning. Performance may drop on non-English text, non-Latin scripts and domains unlike its training data. It lists failure modes such as missing uncommon names, over-redacting public entities, shifted span boundaries and missing novel secret formats, and advises evaluating in your own domain and keeping human review for high-sensitivity work.
