---
id: task/speech-recognition
type: task
slug: speech-recognition
schema_version: 3.1
title: Speech Recognition
group: task-group/audio
order: 1
summary: Turn spoken audio into written text.
hf_pipeline_tags:
- automatic-speech-recognition
inputs:
- audio
outputs:
- text
tags:
- speech
- transcription
- audio
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/automatic-speech-recognition
  title: Hugging Face — Automatic Speech Recognition task
  trust: 0.9
---

## What It Is

Automatic speech recognition (ASR) **writes down the words spoken in a recording**.

- **Transcription:** the output is text in the same language as the speech.
- **Speech translation:** some models write the transcript directly in another language.
- **Timestamps:** many models mark when each word or segment was spoken, which subtitles need.

## When To Use

- **Use it** for meeting notes, subtitles, voice commands and making recordings searchable.
- **Use a speech-language model instead** when you want answers about a recording, not its full transcript.
- **Use audio classification instead** when you only need a label, such as the language or a wake word.

## How To Choose

- **Language coverage:** accuracy varies widely between languages and accents.
- **Word error rate:** the share of words transcribed wrongly, measured on audio like yours.
- **Streaming and speed:** live captioning needs a small model that emits text as audio arrives.
- **Extras:** timestamps, punctuation and telling speakers apart are not always included.
