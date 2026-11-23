---
id: task/text-to-speech
type: task
slug: text-to-speech
schema_version: 3.1
title: Text-to-Speech
group: task-group/audio
order: 2
summary: Read written text aloud in a natural-sounding voice, including voices cloned from a short sample.
hf_pipeline_tags:
- text-to-speech
inputs:
- text
outputs:
- audio
tags:
- speech
- voice
- audio
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/text-to-speech
  title: Hugging Face — Text-to-Speech task
  trust: 0.9
---

## What It Is

A text-to-speech (TTS) model **converts written text into spoken audio**.

- **Preset voices:** the model ships with a fixed set of speakers to pick from.
- **Voice cloning:** a few seconds of reference audio let the model imitate a specific speaker.
- **Style control:** some models accept cues for emotion, pace or emphasis.

## When To Use

- **Use it** for voice assistants, audiobooks, accessibility features and narration.
- **Use music and sound generation instead** when the output is music or effects, not a voice.
- **Use audio enhancement and conversion instead** to change the voice of an existing recording.

## How To Choose

- **Naturalness:** how human the rhythm and intonation sound over long passages.
- **Languages and voices:** which languages are covered and how many speakers are available.
- **Cloning support:** whether a new voice needs a short sample or full fine-tuning, and the speaker's consent.
- **Latency:** conversational use needs audio to start streaming almost at once.
