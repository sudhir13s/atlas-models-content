---
id: task/audio-classification
type: task
slug: audio-classification
schema_version: 3.1
title: Audio Classification
group: task-group/audio
order: 4
summary: Assign a label to an audio clip, such as the sound heard, the language spoken or the speaker's emotion.
hf_pipeline_tags:
- audio-classification
inputs:
- audio
outputs:
- label
tags:
- audio
- classification
- sound-events
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/audio-classification
  title: Hugging Face — Audio Classification task
  trust: 0.9
---

## What It Is

An audio classification model listens to a clip and **picks one or more labels from a fixed set**.

- **Sound events:** recognise a siren, a dog bark, breaking glass or applause.
- **Language identification:** name the language being spoken before routing the audio onward.
- **Emotion recognition:** estimate the speaker's mood from tone of voice.
- **Keyword spotting:** detect a small set of spoken commands or a wake word.

## When To Use

- **Use it** when the answer is a category and the list of categories is fixed in advance.
- **Use speech recognition instead** when you need the words that were spoken.
- **Use a speech-language model instead** when the question is open-ended and no fixed label set exists.

## How To Choose

- **Label set:** the classes the model was trained on must match yours, or you must fine-tune.
- **Clip length and sample rate:** the audio format the model expects as input.
- **Single or multiple labels:** whether overlapping sounds can each be reported.
- **Size and latency:** always-on detection on a device needs a very small model.
