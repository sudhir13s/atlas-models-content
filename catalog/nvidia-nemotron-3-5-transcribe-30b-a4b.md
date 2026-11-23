---
id: model/nvidia-nemotron-3-5-transcribe-30b-a4b
type: model
slug: nvidia-nemotron-3-5-transcribe-30b-a4b
schema_version: 1.1
title: NVIDIA-Nemotron-3.5-Transcribe-30B-A4B
organization: organization/nvidia
release_date: '2026-10-07'
release_year: 2026
release_month: 10
license_note: openmdw-1.1
model_category: audio
tasks:
- task/speech-recognition
parameters_active_b: 4.3
total_parameters: 33697781029
base_models:
- name: nvidia/NVIDIA-Nemotron-3.5-Lightning-30B-A3B-BF16
  address: https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Lightning-30B-A3B-BF16
summary: 'NVIDIA Nemotron-3.5-Transcribe is an offline speech and audio model from NVIDIA: it takes a recording, plus a text instruction and optional context, and returns text. The card lists transcription, speech translation, speaker-attributed transcription, timestamp prediction, language identification and audio question answering, in 40 languages, for recordings of up to one hour.'
intended_use: The card names developers and researchers building speech interfaces, transcription and translation tools, meeting and media processing, customer-service analytics and audio-understanding applications. It says speaker labels distinguish speakers within a recording and do not establish real-world identity, and that it is an offline model rather than a real-time streaming service.
what_sets_it_apart: 'The card describes a Speech-Augmented Language Model: audio features from a transformer speech encoder and a speaker encoder are combined with the text prompt and fed to a language model that generates the text. The language model is a Mamba-2 and Transformer hybrid mixture of experts, and the card gives the whole model, audio encoder included, as 34 billion total parameters with 4.3 billion active. The task and style are chosen by the text instruction.'
running_it: The card says to choose the transcription style with the instruction in the user message and to keep the system prompt empty for transcription and translation. It recommends temperature 0 with reasoning off for transcription, translation and language identification, and temperature 0.6, top-p 0.95 with reasoning on for audio understanding. It lists one H100 80 GB GPU for deployment.
pipeline:
  source: https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B
  summary: A transformer speech encoder turns the recording into vectors, and a separate speaker encoder (left) marks who is speaking when and adds that to the same vectors. A projection maps them to the language model's width, they join the text instruction, and a hybrid Mamba-2, attention and mixture-of-experts (MoE) language model writes the text.
  side_into: speaker-fusion
  stages:
  - id: side-audio
    role: input
    title: The same audio
    side: true
  - id: speaker-encoder
    role: audioEncoder
    title: Sortformer speaker encoder
    details:
    - Nemotron-3-Diarization · frozen
    note: A diarization model of about 99M parameters that predicts, for every frame, which of up to eight speakers is talking.
    side: true
  - id: speaker-activity
    role: step
    title: Speaker activity
    details:
    - 8 speakers · threshold 0.5
    note: The activity of each speaker is turned into 0 or 1 at a threshold of 0.5 and multiplied with a fixed sinusoidal vector per speaker.
    side: true
  - id: audio
    role: input
    title: Audio
    details:
    - 16 kHz · up to one hour
  - id: mel
    role: step
    title: Log-mel spectrogram
    details:
    - 128 bins · 10 ms hop
  - id: stacking
    role: embedding
    title: Stack 8 frames
    details:
    - one vector per 80 ms
    note: Eight neighbouring frames are joined into one, so the sequence gets eight times shorter. The encoder works on 30-second chunks of these features.
  - id: asr-block
    role: audioEncoder
    title: Attention + feed-forward
    details:
    - 10 heads · width 1280 · RoPE
    frame: Encoder block
    repeat: 32
  - id: speaker-fusion
    role: step
    title: Add speaker information
    details:
    - LayerNorm(speech) + speaker vectors
    note: The normalised speech vectors and the speaker vectors are added, so each frame also carries who is speaking.
  - id: projector
    role: projector
    title: Projection
    details:
    - width 1280 → 2688
    note: An identity connector passes the 1280-wide vectors on, and a learned projection maps them to the width of the language model.
  - id: merge
    role: embedding
    title: Place among text embeddings
    details:
    - at the audio tag in the prompt
    note: The audio vectors take the place of the audio tag in the prompt, so the instruction, any context and the audio form one sequence.
  - id: mamba
    role: attention
    title: Mamba-2 layers
    details:
    - 23 of the 52 layers
    note: State-space layers that read the sequence in order with a fixed-size memory. The three kinds of layer are interleaved in a fixed order set by the configuration.
    frame: Hybrid stack
  - id: moe
    role: experts
    title: MoE feed-forward layers
    details:
    - 23 layers · 128 experts
    - 6 routed + 1 shared per token
    note: A router picks 6 of 128 expert feed-forward networks for each token, and one shared expert always runs.
    frame: Hybrid stack
  - id: attention
    role: attention
    title: Attention layers
    details:
    - 6 layers · 32 heads · 2 KV heads
    frame: Hybrid stack
  - id: output
    role: outputHead
    title: Output head
    details:
    - logits over 131,072
    note: Writes the text one token at a time. Two trained multi-token prediction depths can propose several tokens ahead for speculative decoding, and separate CTC heads can align words to timestamps on request.
filters:
  size_category: large
  is_quantized: false
  is_moe: true
  is_gated: false
  pipeline_tag: automatic-speech-recognition
  library: nemo
hub_signals:
  downloads_30d: 92179
  likes: 3411
  last_modified: '2026-10-29'
  observed_at: '2026-11-21'
  source: https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B
aliases:
- nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B
lineage:
  parents: []
  children: []
quotes:
  summary: NVIDIA Nemotron-3.5-Transcribe is an offline speech and audio model that processes recordings, together with text instructions and optional context, into text responses.
  intended_use: Intended users are developers and researchers building speech interfaces, transcription and translation tools, meeting and media processing, customer-service analytics, and audio-understanding applications.
  what_sets_it_apart: Audio features are combined with the text prompt for text generation.
  running_it: Choose the transcription style with the instruction in the user message; keep the system prompt empty for transcription and translation.
tags:
- automatic-speech-recognition
- nemo-speechlm
- moe
updated: '2026-11-23'
sources:
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B
  title: nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B/blob/main/config.json
  title: nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2310.09424
  title: arXiv:2310.09424
  trust: 0.95
- url: https://github.com/NVIDIA-NeMo/NeMo/blob/main/nemo/collections/asr/modules/parallel_expert_encoder.py
  title: 'NeMo — nemo/collections/asr/modules/parallel_expert_encoder.py: speech and speaker encoder fusion (GitHub)'
  trust: 0.9
---

## Summary

NVIDIA Nemotron-3.5-Transcribe is an offline speech and audio model from NVIDIA: it takes a recording, plus a text instruction and optional context, and returns text. The card lists transcription, speech translation, speaker-attributed transcription, timestamp prediction, language identification and audio question answering, in 40 languages, for recordings of up to one hour.

## Intended Use

The card names developers and researchers building speech interfaces, transcription and translation tools, meeting and media processing, customer-service analytics and audio-understanding applications. It says speaker labels distinguish speakers within a recording and do not establish real-world identity, and that it is an offline model rather than a real-time streaming service.

## What Sets It Apart

The card describes a Speech-Augmented Language Model: audio features from a transformer speech encoder and a speaker encoder are combined with the text prompt and fed to a language model that generates the text. The language model is a Mamba-2 and Transformer hybrid mixture of experts, and the card gives the whole model, audio encoder included, as 34 billion total parameters with 4.3 billion active. The task and style are chosen by the text instruction.

## Running It

The card says to choose the transcription style with the instruction in the user message and to keep the system prompt empty for transcription and translation. It recommends temperature 0 with reasoning off for transcription, translation and language identification, and temperature 0.6, top-p 0.95 with reasoning on for audio understanding. It lists one H100 80 GB GPU for deployment.
