---
id: model/gemma-4-e4b-it
type: model
slug: gemma-4-e4b-it
schema_version: 3.0
title: gemma-4-E4B-it
organization: organization/google-deepmind
release_date: '2026-03-02'
license: license/apache-2-0
model_category: multimodal
model_kind: multimodal
tasks:
- task/any-to-any-models
parameters_active_b: 8.0
total_parameters: 7996156490
context_window_tokens: 131072
base_models:
- name: google/gemma-4-E4B
  address: https://huggingface.co/google/gemma-4-E4B
summary: Gemma 4 E4B IT is the instruction-tuned E4B model of Google DeepMind's Gemma 4 family of open models. It accepts text and image input, plus audio on this size, and writes text as its output. The card gives it 4.5 billion effective parameters (8 billion with embeddings), a 128K-token context window and support for over 140 languages.
intended_use: The card says Gemma 4 is well suited to text generation, coding and reasoning, and to agentic workflows and multimodal understanding. It describes the E2B and E4B sizes as aimed at mobile and edge devices, and all models as having native function calling for building agents.
what_sets_it_apart: The E in E4B stands for effective parameters. The card says the small models use Per-Layer Embeddings, which give each decoder layer its own small embedding for every token, so the large lookup tables add storage but little computation. It also describes an attention design that interleaves local sliding-window layers with global layers, configurable thinking modes, and native support for a system role.
fingerprint:
  attention_type: GQA
  num_layers: 42
  attention_heads: 8
  kv_heads: 2
  head_size: 256
  hidden_size: 2560
  intermediate_size: 10240
  vocabulary_size: 262144
  normalization: RMSNorm
  positional_encoding: RoPE
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gemma4/modeling_gemma4.py
  summary: A vision encoder turns an image into a short row of vectors and an audio encoder does the same for sound (left); each has a projector that maps its vectors to the language model's width. They take their places among the text token embeddings, and a 42-layer language model reads the whole sequence and writes text.
  side_into: merge
  stages:
  - id: image
    role: input
    title: Image
    side: true
    into: merge
  - id: vision-encoder
    role: visionEncoder
    title: Vision encoder
    details:
    - 16 × 16 pixel patches
    - 16 blocks · width 768
    note: A linear layer turns the pixels of each 16 by 16 patch into one vector, and learned x and y position embeddings are added. Sixteen attention and feed-forward blocks (12 heads, width 768) then read the patch vectors.
    side: true
  - id: pool
    role: step
    title: Average 3 × 3 patches
    details:
    - 280 soft tokens by default
    note: Each 3 by 3 group of neighbouring patch vectors is averaged into one, so the image becomes nine times fewer vectors; the configuration's default is 280 per image.
    side: true
  - id: vision-projector
    role: projector
    title: Projector
    details:
    - RMSNorm · linear 768 → 2560
    note: Normalizes the image vectors and maps them into the space of the language model's token embeddings.
    side: true
  - id: audio
    role: input
    title: Audio
    side: true
    into: merge
  - id: audio-encoder
    role: audioEncoder
    title: Audio encoder
    details:
    - 12 layers
    side: true
  - id: audio-projector
    role: projector
    title: Projector
    note: Maps the audio vectors into the space of the language model's token embeddings.
    side: true
  - id: text
    role: tokenizer
    title: Text tokens
    details:
    - vocabulary 262,144
  - id: embedding
    role: embedding
    title: Token embedding
    details:
    - width 2560
  - id: merge
    role: embedding
    title: Place among text embeddings
    details:
    - at the image and audio positions
    note: The image vectors take the places the prompt reserves with image tokens, and the audio vectors are placed at the audio token positions the same way.
  - id: block
    role: attention
    title: Attention + feed-forward
    details:
    - 8 heads · 2 KV heads · width 2560
    - '5 sliding-window : 1 global'
    note: Five layers attend over a sliding window of 512 tokens, then one layer attends over the whole sequence, and the pattern repeats. Each layer also adds a small per-layer embedding of the token (width 256), and the last 18 layers reuse the keys and values of earlier layers.
    frame: Transformer block
    repeat: 42
  - id: norm
    role: normalisation
    title: Final RMSNorm
  - id: output
    role: outputHead
    title: Output head
    details:
    - logits over 262,144 · cap 30
    note: Shares its weights with the token embedding. The logits are soft-capped at 30 before sampling.
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: any-to-any
  library: transformers
hub_signals:
  downloads_30d: 4326816
  likes: 1890
  last_modified: '2026-11-03'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/gemma-4-E4B-it
aliases:
- google/gemma-4-E4B-it
lineage:
  parents: []
  children: []
quotes:
  summary: Gemma 4 models are multimodal, handling text and image input (with audio supported on E2B, E4B, and 12B) and generating text output.
  intended_use: Gemma 4 is well-suited for tasks like text generation, coding, and reasoning.
  what_sets_it_apart: The "E" in E2B and E4B stands for "effective" parameters.
tags:
- gemma4
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/gemma-4-E4B-it
  title: google/gemma-4-E4B-it — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/gemma-4-E4B-it/blob/main/config.json
  title: google/gemma-4-E4B-it — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2607.02770
  title: arXiv:2607.02770
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gemma4/modeling_gemma4.py
  title: 'transformers/models/gemma4/modeling_gemma4.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


