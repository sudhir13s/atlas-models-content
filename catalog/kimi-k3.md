---
id: model/kimi-k3
type: model
slug: kimi-k3
schema_version: 1.1
title: Kimi-K3
organization: organization/moonshot-ai
release_date: '2026-06-13'
release_year: 2026
release_month: 6
license_note: kimi-k3
model_category: language
tasks:
- task/chat-models
- task/vision-language-models
total_parameters: 2779931837184
context_window_tokens: 1048576
summary: Kimi K3 is an open-weight language model from Moonshot AI that reads text, images and video and is built for agentic work, where a model plans and uses tools over many steps. The card gives it 2.8 trillion parameters in total, 104 billion of them activated for each token, and a context window of 1 million tokens. The weights are released under the Kimi K3 License.
intended_use: The card says it is designed for long-horizon coding, knowledge work and reasoning. For coding it describes long engineering sessions with minimal human oversight, and for knowledge work it describes deep research with interactive visualizations and dashboards.
what_sets_it_apart: The card says it is built on Kimi Delta Attention (KDA) and Attention Residuals (AttnRes), two attention designs by the maker. It is a mixture-of-experts model that activates 16 of 896 experts per token, and the card claims this gives about 2.5 times the overall scaling efficiency of Kimi K2.
running_it: The card says the model can be reached through Moonshot's API, which is compatible with the OpenAI and Anthropic formats. For running it yourself it recommends the vLLM and SGLang inference engines and links their recipes.
fingerprint:
  attention_type: MLA
  num_layers: 93
  attention_heads: 96
  kv_heads: 96
  hidden_size: 7168
  intermediate_size: 33792
  context_window_tokens: 1048576
  vocabulary_size: 163840
  activation_function: situ
  normalization: RMSNorm
moe_config:
  expert_count: 896
  shared_expert: true
pipeline:
  source: https://huggingface.co/moonshotai/Kimi-K3/blob/main/modeling_kimi_k3.py
  summary: A vision encoder turns an image or video into vectors, a patch merger maps them to the language model's width, and the language model reads them in the same sequence as the text tokens and writes text. The public code file covers the vision side; the inside of the language model's layers is not in it, so it is not drawn.
  side_into: merge
  stages:
  - id: text
    role: tokenizer
    title: Text tokens
    details:
    - vocabulary 163,840
    side: true
  - id: image
    role: input
    title: Image or video
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 14 × 14 pixel patches · width 1024
  - id: vision-block
    role: visionEncoder
    title: Attention + feed-forward
    details:
    - 12 heads
    frame: Vision block
    repeat: 27
  - id: merger
    role: projector
    title: Patch merger
    details:
    - to width 7168
    note: Joins neighbouring patch vectors and maps them to the width of the language model.
  - id: merge
    role: embedding
    title: Place among text embeddings
    details:
    - at the media placeholder positions
    note: The image vectors take the places the prompt reserves with media placeholder tokens, so text and image form one sequence.
  - id: language-model
    role: attention
    title: Language model
    details:
    - 93 layers · width 7168
    note: A causal language model writes the answer one token at a time. Its layers are loaded from an implementation that is not part of the public code file, so their inside is not drawn.
  - id: output
    role: outputHead
    title: Text output
filters:
  size_category: xlarge
  is_quantized: false
  is_moe: true
  attention_type: MLA
  is_gated: false
  pipeline_tag: image-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 734802
  likes: 12006
  last_modified: '2026-11-20'
  observed_at: '2026-11-21'
  source: https://huggingface.co/moonshotai/Kimi-K3
aliases:
- moonshotai/Kimi-K3
lineage:
  parents: []
  children: []
quotes:
  summary: Kimi K3 is an open-weight, native multimodal agentic model
  intended_use: designed for frontier intelligence across long-horizon coding, knowledge work, and reasoning.
  what_sets_it_apart: activates 16 out of 896 experts
  running_it: 'Currently, Kimi K3 is recommended to run on the following inference engines:'
tags:
- llm
- image-text-to-text
- kimi-k3
- moe
- mla
updated: '2026-11-23'
sources:
- url: https://huggingface.co/moonshotai/Kimi-K3
  title: moonshotai/Kimi-K3 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/moonshotai/Kimi-K3/blob/main/config.json
  title: moonshotai/Kimi-K3 — config.json
  trust: 0.9
- url: https://huggingface.co/moonshotai/Kimi-K3/blob/main/modeling_kimi_k3.py
  title: moonshotai/Kimi-K3 — modeling_kimi_k3.py
  trust: 0.9
---

## Summary

Kimi K3 is an open-weight language model from Moonshot AI that reads text, images and video and is built for agentic work, where a model plans and uses tools over many steps. The card gives it 2.8 trillion parameters in total, 104 billion of them activated for each token, and a context window of 1 million tokens. The weights are released under the Kimi K3 License.

## Intended Use

The card says it is designed for long-horizon coding, knowledge work and reasoning. For coding it describes long engineering sessions with minimal human oversight, and for knowledge work it describes deep research with interactive visualizations and dashboards.

## What Sets It Apart

The card says it is built on Kimi Delta Attention (KDA) and Attention Residuals (AttnRes), two attention designs by the maker. It is a mixture-of-experts model that activates 16 of 896 experts per token, and the card claims this gives about 2.5 times the overall scaling efficiency of Kimi K2.

## Running It

The card says the model can be reached through Moonshot's API, which is compatible with the OpenAI and Anthropic formats. For running it yourself it recommends the vLLM and SGLang inference engines and links their recipes.
