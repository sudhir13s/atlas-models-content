---
id: task/speech-language-models
type: task
slug: speech-language-models
schema_version: 1.0
title: "Speech-Language Models"
group: task-group/audio
order: 3
summary: "Answer questions about a recording, or take spoken input directly, and reply in text."
hf_pipeline_tags: [audio-text-to-text]
inputs: [audio, text]
outputs: [text]
tags: [speech, audio, llm]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/audio-text-to-text", title: "Hugging Face — Audio-Text-to-Text task", trust: 0.9 }
---

## What It Is
A speech-language model is a language model that **hears audio directly, with no transcript in between**.

- **Audio question answering:** ask what was said, who spoke, or what happened in a recording.
- **Spoken prompts:** the user talks and the model answers the request in text.
- **Beyond words:** tone, background sounds and music can inform the answer.

## When To Use
- **Use it** when the answer depends on understanding audio, not on a word-for-word transcript.
- **Use speech recognition instead** when you need the exact transcript, timestamps or subtitles.
- **Use an any-to-any model instead** when the reply must be spoken audio, not text.

## How To Choose
- **Audio types understood:** speech only, or also music and environmental sound.
- **Audio length and languages:** how many minutes fit in one request, and which spoken languages are covered.
- **Reasoning quality:** the strength of the underlying language model on the questions you ask.
- **Size against hardware:** parameter count and quantization decide whether it fits in memory.
