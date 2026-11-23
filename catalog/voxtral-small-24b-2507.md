---
id: model/voxtral-small-24b-2507
type: model
slug: voxtral-small-24b-2507
schema_version: 3.0
title: Voxtral-Small-24B-2507
organization: organization/mistral-ai
release_date: '2025-07-01'
license: license/apache-2-0
model_category: audio
model_kind: audio-speech
tasks:
- task/speech-language-models
parameters_active_b: 24.26
total_parameters: 24261800960
context_window_tokens: 32768
base_models:
- name: mistralai/Mistral-Small-24B-Base-2501
  address: https://huggingface.co/mistralai/Mistral-Small-24B-Base-2501
summary: 'Voxtral Small 1.0 (24B) 2507 is an audio-language model from Mistral AI: it takes speech or other audio, optionally with text, and answers in text. The card describes it as an enhancement of Mistral Small 3 with audio input, for speech transcription, translation and audio understanding. With a 32k-token context it handles audio up to 30 minutes for transcription or 40 minutes for understanding, and the card lists English, French, German, Spanish, Italian, Portuguese, Dutch and Hindi.'
intended_use: The card names speech transcription, translation and audio understanding. It also lists asking questions about audio directly, summarizing audio without separate speech-recognition and language models, and triggering backend functions or API calls from spoken requests.
what_sets_it_apart: Mistral says it has a dedicated transcription mode, which by default detects the spoken language itself and transcribes in that language. It says the model keeps the text abilities of its language model backbone and can call functions straight from voice, so one model covers what would otherwise need a separate speech recognizer and language model.
running_it: The card recommends vLLM, version 0.10.0 or later, and says running the model on GPU needs about 55 GB of GPU memory in bf16 or fp16. It suggests temperature 0.2 and top_p 0.95 for chat and audio understanding, and temperature 0.0 for transcription.
limitations: The card says system prompts are not yet supported.
fingerprint:
  attention_type: GQA
  num_layers: 40
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 5120
  intermediate_size: 32768
  vocabulary_size: 131072
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 100000000.0
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/voxtral/modeling_voxtral.py
  summary: An audio encoder turns sound into vectors, a small projector maps them to the language model's width, and the language model reads them in the same sequence as the text tokens and writes text.
  side_into: merge
  stages:
  - id: text
    role: tokenizer
    title: Text tokens
    side: true
  - id: text-embedding
    role: embedding
    title: Token embedding
    details:
    - width 5120
    side: true
  - id: audio
    role: input
    title: Audio
    details:
    - log-mel spectrogram · 128 bins
  - id: stem
    role: audioEncoder
    title: Convolution layers
    details:
    - two layers · width 1280
    note: Two convolution layers turn the spectrogram into a sequence of vectors, and position embeddings are added.
  - id: audio-block
    role: audioEncoder
    title: Attention + feed-forward
    details:
    - 20 heads · norm before each part
    frame: Encoder block
    repeat: 32
  - id: audio-norm
    role: normalisation
    title: Final LayerNorm
  - id: group
    role: step
    title: Join 4 frames
    details:
    - width 1280 → 5120
    note: Four neighbouring audio vectors are joined into one, so the sequence gets four times shorter.
  - id: projector
    role: projector
    title: Projector
    details:
    - linear · GELU · linear
    note: Maps the audio vectors into the space of the language model's token embeddings.
  - id: merge
    role: embedding
    title: Place among text embeddings
    details:
    - at the audio token positions
    note: The audio vectors take the places the prompt reserves with audio tokens, so text and audio form one sequence.
  - id: language-block
    role: attention
    title: Attention + feed-forward
    details:
    - 32 heads · 8 KV heads · width 5120
    frame: Transformer block
    repeat: 40
  - id: output
    role: outputHead
    title: Output head
    details:
    - logits over 131,072
filters:
  size_category: large
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: audio-text-to-text
  library: vllm
hub_signals:
  downloads_30d: 7652
  likes: 557
  last_modified: '2025-12-20'
  observed_at: '2026-11-21'
  source: https://huggingface.co/mistralai/Voxtral-Small-24B-2507
aliases:
- mistralai/Voxtral-Small-24B-2507
lineage:
  parents: []
  children: []
quotes:
  summary: With a 32k token context length, Voxtral handles audios up to 30 minutes for transcription, or 40 minutes for understanding
  intended_use: Supports asking questions directly through audio.
  what_sets_it_apart: Voxtral can operate in a pure speech transcription mode to maximize performance.
  running_it: '`temperature=0.2` and `top_p=0.95` for chat completion (*e.g. Audio Understanding*) and `temperature=0.0` for transcription'
  limitations: System prompts are not yet supported
locked:
- context_window_tokens
tags:
- voxtral
updated: '2026-11-23'
sources:
- url: https://huggingface.co/mistralai/Voxtral-Small-24B-2507
  title: mistralai/Voxtral-Small-24B-2507 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/mistralai/Voxtral-Small-24B-2507/blob/main/config.json
  title: mistralai/Voxtral-Small-24B-2507 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2507.13264
  title: arXiv:2507.13264
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/voxtral/modeling_voxtral.py
  title: 'transformers/models/voxtral/modeling_voxtral.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


