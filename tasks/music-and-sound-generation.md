---
id: task/music-and-sound-generation
type: task
slug: music-and-sound-generation
schema_version: 3.1
title: Music and Sound Generation
group: task-group/generation
order: 5
summary: Create music or sound effects from a text description; speech is handled by a separate task.
hf_pipeline_tags:
- text-to-audio
inputs:
- text
outputs:
- audio
tags:
- generative
- audio
- music
updated: '2026-11-18'
sources:
- url: https://huggingface.co/models?pipeline_tag=text-to-audio
  title: Hugging Face — models tagged text-to-audio
  trust: 0.9
---

## What It Is

A music and sound generation model **turns a written description into non-speech audio**.

- **Music:** instrumental tracks or songs in a requested genre, mood and tempo.
- **Sound effects:** short clips such as footsteps, rain or machinery.
- **Ambience:** longer background textures for video, games or installations.

## When To Use

- **Use it** when you need original music or effects described in words.
- **Use text-to-speech instead** when the output is a voice reading a script.
- **Use audio enhancement and conversion instead** when you already have a recording to clean or transform.

## How To Choose

- **Content type:** music, effects and ambience are usually served by different models.
- **Clip length and audio quality:** the longest audio per pass, the sample rate, and mono or stereo output.
- **Control:** whether tempo, lyrics, a melody or a reference clip can steer the result.
- **Licence:** both the weights and the training data affect whether outputs can be used commercially.
