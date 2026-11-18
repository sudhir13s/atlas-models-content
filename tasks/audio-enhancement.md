---
id: task/audio-enhancement
type: task
slug: audio-enhancement
schema_version: 1.0
title: "Audio Enhancement and Conversion"
group: task-group/audio
order: 5
summary: "Clean, separate or transform an existing recording into a new recording."
hf_pipeline_tags: [audio-to-audio]
inputs: [audio]
outputs: [audio]
tags: [audio, denoising, source-separation]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/audio-to-audio", title: "Hugging Face — Audio-to-Audio task", trust: 0.9 }
---

## What It Is
An audio enhancement model takes a recording in and **returns altered audio of the same content**.

- **Denoising:** remove background noise, hum or echo so speech is clearer.
- **Source separation:** split a mix into its parts, such as vocals and instruments, or individual speakers.
- **Voice conversion:** keep the words and timing but change who appears to be speaking.

## When To Use
- **Use it** when a recording already exists and needs cleaning, splitting or transforming.
- **Use text-to-speech instead** when the voice should be produced from a script, not a recording.
- **Use music and sound generation instead** when you need new audio made from a description.

## How To Choose
- **Operation supported:** denoising, separation and conversion are separate models.
- **Audio domain:** models trained on speech handle music poorly, and the reverse.
- **Sample rate:** output quality is capped by the rate the model works at.
- **Real-time capability:** live calls need low delay; offline clean-up can use a heavier model.
