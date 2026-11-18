---
id: task/video-understanding
type: task
slug: video-understanding
schema_version: 1.0
title: "Video Understanding"
group: task-group/vision
order: 7
summary: "Classify what happens in a video clip or answer text questions about it."
hf_pipeline_tags: [video-classification, video-text-to-text]
inputs: [video, text]
outputs: [text, label]
tags: [video, multimodal, classification]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/video-classification", title: "Hugging Face — Video Classification task", trust: 0.9 }
---

## What It Is
A video understanding model reads a sequence of frames and **reports what happens over time**.

- **Video classification** assigns a label, usually an action or activity, to a clip.
- **Video question answering** takes a clip plus a text prompt and writes a reply.
- **Frame sampling:** most models see only a selection of frames, not every one.

## When To Use
- **Use it** when the answer depends on motion or the order of events.
- **Use a vision-language model instead** when a single frame holds the answer; it is cheaper.
- **Use object detection instead**, frame by frame, when you need positions of items rather than a description.
- **Use speech recognition as well** when the spoken audio carries the information.

## How To Choose
- **Clip length:** how many frames, and so how many seconds, the model can take in one pass.
- **Output type:** fixed action labels or free-form text answers.
- **Frame resolution:** small on-screen text and distant detail need more pixels per frame.
- **Size against hardware:** many frames multiply memory use and processing time.
