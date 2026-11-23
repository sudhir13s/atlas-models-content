---
id: model/vibevoice-1-5b
type: model
slug: vibevoice-1-5b
schema_version: 3.0
title: VibeVoice-1.5B
organization: organization/microsoft
release_date: '2025-08-25'
license: license/mit
model_category: audio
model_kind: audio-speech
tasks:
- task/text-to-speech
parameters_active_b: 2.7
total_parameters: 2704021985
summary: VibeVoice-1.5B is a text-to-speech model from Microsoft that turns written text into spoken audio, aimed at long conversations with several speakers, such as podcasts. The card says it can produce up to 90 minutes of speech with up to 4 distinct speakers. It was trained on English and Chinese data only.
intended_use: 'The card limits the model to research use exploring realistic audio dialogue generation, and does not recommend commercial or real-world applications without further testing. It lists out-of-scope uses: voice impersonation without recorded consent, disinformation, real-time voice conversion, unsupported languages, music or background sound, and generating text transcripts.'
what_sets_it_apart: The card says it uses continuous speech tokenizers, an acoustic one and a semantic one, running at an ultra-low frame rate of 7.5 Hz, which makes long sequences cheaper to process. A large language model (LLM), Qwen2.5-1.5B in this release, reads the text and the dialogue flow, and a diffusion head generates the fine acoustic detail, a next-token diffusion framework.
pipeline:
  source: https://huggingface.co/microsoft/VibeVoice-1.5B
  summary: A script with speaker turns and short voice samples (left) go into one language model. Each time the model asks for speech, a small diffusion head turns its last hidden state into one frame of acoustic latents, and the acoustic decoder turns those into sound. Speech comes out at 7.5 frames per second, and each new frame is fed back as the next input.
  side_into: merge
  stages:
  - id: voice
    role: input
    title: Voice samples
    details:
    - one per speaker
    side: true
  - id: acoustic-encoder
    role: autoencoder
    title: Acoustic tokenizer encoder
    details:
    - σ-VAE · 3200× fewer steps
    note: The encoder half of the acoustic tokenizer turns 24 kHz audio into continuous latents at 7.5 frames per second, 64 numbers per frame. It was pre-trained separately and is frozen.
    side: true
  - id: acoustic-connector
    role: projector
    title: Acoustic connector
    details:
    - linear · RMSNorm · linear
    note: Maps each latent frame to the width of the language model.
    side: true
  - id: script
    role: tokenizer
    title: Script tokens
    details:
    - text with speaker turns
  - id: text-embedding
    role: embedding
    title: Token embedding
    details:
    - width 1536
  - id: merge
    role: embedding
    title: Place among text embeddings
    details:
    - at the speech token positions
    note: The voice-sample vectors take the places reserved for them in the prompt, so text and speech form one sequence.
  - id: language-block
    role: attention
    title: Attention + feed-forward
    details:
    - Qwen2.5-1.5B · 12 heads · 2 KV heads
    frame: Transformer block
    repeat: 28
  - id: lm-head
    role: outputHead
    title: Next token
    details:
    - speech start, speech frame or end
    note: A linear head picks the next token, limited to four choices (speech start, speech frame, speech end, end of text). When it is a speech frame, the hidden state at that position becomes the condition for the diffusion head.
  - id: diffusion-head
    role: denoiser
    title: Diffusion head
    details:
    - 4 layers · reads the hidden state
    note: A small network, about 123M parameters per the card, that predicts the noise in one frame of acoustic latents from the noisy frame, the step and the language model's hidden state. It runs with and without the condition for classifier-free guidance.
    frame: Diffusion loop
  - id: scheduler
    role: step
    title: Scheduler step
    details:
    - DPM-Solver
    note: Uses the prediction to make the latent frame a little cleaner; the loop repeats for a set number of steps.
    frame: Diffusion loop
  - id: acoustic-decoder
    role: autoencoder
    title: Acoustic tokenizer decoder
    details:
    - latents to waveform
    note: Turns the clean latent frame into audio. The same frame also goes through the semantic tokenizer encoder and both connectors, and the sum is fed back to the language model as its next input.
  - id: audio
    role: outputHead
    title: Speech
    details:
    - 24 kHz · up to 4 speakers
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-to-speech
  library: transformers
hub_signals:
  downloads_30d: 214324
  likes: 2522
  last_modified: '2026-01-22'
  observed_at: '2026-11-21'
  source: https://huggingface.co/microsoft/VibeVoice-1.5B
aliases:
- microsoft/VibeVoice-1.5B
lineage:
  parents: []
  children: []
quotes:
  summary: generating expressive, long-form, multi-speaker conversational audio, such as podcasts, from text.
  intended_use: The VibeVoice model is limited to research purpose use exploring highly realistic audio dialogue generation
  what_sets_it_apart: VibeVoice employs a next-token diffusion framework, leveraging a Large Language Model (LLM) to understand textual context and dialogue flow, and a diffusion head to generate high-fidelity acoustic details.
tags:
- vibevoice
updated: '2026-11-23'
sources:
- url: https://huggingface.co/microsoft/VibeVoice-1.5B
  title: microsoft/VibeVoice-1.5B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/VibeVoice-1.5B/blob/main/config.json
  title: microsoft/VibeVoice-1.5B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2508.19205
  title: arXiv:2508.19205
  trust: 0.95
- url: https://arxiv.org/abs/2412.08635
  title: arXiv:2412.08635
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vibevoice/modeling_vibevoice.py
  title: 'transformers/models/vibevoice/modeling_vibevoice.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vibevoice/generation_vibevoice.py
  title: 'transformers/models/vibevoice/generation_vibevoice.py: generation loop (Hugging Face Transformers)'
  trust: 0.9
---


