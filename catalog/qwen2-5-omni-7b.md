---
id: model/qwen2-5-omni-7b
type: model
slug: qwen2-5-omni-7b
schema_version: 3.0
title: Qwen2.5-Omni-7B
organization: organization/qwen
release_date: '2025-03-22'
license: license/apache-2-0
model_category: multimodal
model_kind: multimodal
tasks:
- task/any-to-any-models
parameters_active_b: 10.73
total_parameters: 10732225440
summary: Qwen2.5-Omni-7B is a multimodal model from Qwen that perceives text, images, audio and video as input and answers with text or with spoken audio. The card describes it as end-to-end, meaning one model handles all of these, and as able to generate text and natural speech responses in a streaming manner, that is, as the input arrives.
intended_use: The card presents it for real-time voice and video chat, with an architecture that takes chunked input and returns output immediately. It also describes following spoken instructions end to end, and reports results on speech recognition, translation, audio understanding, image reasoning and video understanding.
what_sets_it_apart: The card describes a Thinker-Talker architecture proposed by the Qwen team. It also describes TMRoPE (Time-aligned Multimodal RoPE), a position embedding that synchronizes the timestamps of video input with audio.
pipeline:
  source: https://arxiv.org/abs/2503.20215
  summary: The Thinker, a language model with audio and vision encoders, reads text, audio, images and video as one sequence and writes the text answer. The Talker reads the Thinker's hidden states and the text it samples and writes speech codes, which a flow-matching DiT (diffusion transformer) turns into a mel-spectrogram and a vocoder turns into a waveform. The vision and audio encoders are on the left, the speech output on the right.
  side_into: merge
  branch_from: thinker-block
  stages:
  - id: image
    role: input
    title: Image or video
    side: true
    into: merge
  - id: vision-encoder
    role: visionEncoder
    title: Vision encoder
    details:
    - 32 layers
    - 14 × 14 pixel patches
    note: Images and video frames are read by the Qwen2.5-VL vision encoder.
    side: true
  - id: audio
    role: input
    title: Audio
    details:
    - 16 kHz · mel-spectrogram, 128 bins
    side: true
    into: merge
  - id: audio-encoder
    role: audioEncoder
    title: Audio encoder
    details:
    - 32 blocks · 20 heads
    - width 1280 → 3584
    note: The audio encoder comes from Qwen2-Audio. Each output vector covers about 40 ms of sound, and a final projection maps it to width 3584.
    side: true
  - id: text
    role: tokenizer
    title: Text tokens
    details:
    - byte-level BPE · 151,643 tokens
  - id: embedding
    role: embedding
    title: Token embedding
    details:
    - width 3584
  - id: merge
    role: embedding
    title: Place among text embeddings
    details:
    - TMRoPE · 1 time step = 40 ms
    note: Audio, image and video vectors take their places in the token sequence. Time-aligned multimodal RoPE (TMRoPE) gives audio and video frames time positions so they line up.
  - id: thinker-block
    role: attention
    title: Attention + feed-forward
    details:
    - 28 heads · 4 KV heads · width 3584
    frame: Thinker block
    repeat: 28
  - id: text-head
    role: outputHead
    title: Text head
    details:
    - text answer · logits over 152,064
    note: The Thinker writes the text answer here. Its hidden states and the embeddings of the text tokens it samples go on to the Talker.
  - id: talker-block
    role: attention
    title: Talker
    details:
    - 24 blocks
    - 12 heads · 4 KV heads · width 896
    note: The Talker is a dual-track autoregressive decoder. It shares the Thinker's context and writes discrete speech codes of the qwen-tts-tokenizer codec.
    branch: true
  - id: dit
    role: denoiser
    title: Flow-matching DiT
    details:
    - codes to mel-spectrogram · 22 blocks
    note: Turns the speech codes into a mel-spectrogram chunk by chunk. Its attention sees 4 blocks of codes, 2 back and 1 ahead, so speech can stream.
    branch: true
  - id: vocoder
    role: step
    title: BigVGAN vocoder
    details:
    - mel-spectrogram to waveform
    note: A modified BigVGAN turns the mel-spectrogram into the sound wave, also chunk by chunk.
    branch: true
  - id: speech
    role: outputHead
    title: Speech
    branch: true
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: any-to-any
  library: transformers
hub_signals:
  downloads_30d: 253435
  likes: 1980
  last_modified: '2025-04-30'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Qwen/Qwen2.5-Omni-7B
aliases:
- Qwen/Qwen2.5-Omni-7B
lineage:
  parents: []
  children: []
quotes:
  summary: Qwen2.5-Omni is an end-to-end multimodal model designed to perceive diverse modalities, including text, images, audio, and video, while simultaneously generating text and natural speech responses in a streaming manner.
  intended_use: Architecture designed for fully real-time interactions, supporting chunked input and immediate output.
  what_sets_it_apart: We propose a novel position embedding, named TMRoPE (Time-aligned Multimodal RoPE), to synchronize the timestamps of video inputs with audio.
tags:
- qwen2-5-omni
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-Omni-7B
  title: Qwen/Qwen2.5-Omni-7B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-Omni-7B/blob/main/config.json
  title: Qwen/Qwen2.5-Omni-7B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2503.20215
  title: arXiv:2503.20215
  trust: 0.95
---


