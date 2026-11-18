---
id: task/video-generation
type: task
slug: video-generation
schema_version: 1.0
title: "Video Generation"
group: task-group/generation
order: 3
summary: "Create a short video clip from a text prompt, a starting image or an existing video."
hf_pipeline_tags: [text-to-video, image-to-video, image-text-to-video, video-to-video]
inputs: [text, image, video]
outputs: [video]
tags: [generative, video, diffusion]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/text-to-video", title: "Hugging Face — Text-to-Video task", trust: 0.9 }
---

## What It Is
A video generation model **produces a sequence of frames that play as one coherent clip**.

- **Text-to-video:** a written prompt describes the scene, the motion and the camera movement.
- **Image-to-video:** a still image becomes the first frame and the model animates it.
- **Video-to-video:** an existing clip is restyled or altered while its motion is kept.

## When To Use
- **Use it** when the result must show motion over time, not a single frame.
- **Use image generation instead** when a still picture is enough; it is far cheaper and faster.
- **Use image editing and upscaling instead** when only individual frames need retouching.

## How To Choose
- **Clip length and frame rate:** the longest clip the model produces in one pass.
- **Temporal consistency:** whether subjects keep their shape and identity from frame to frame.
- **Conditioning options:** text only, a first frame, a last frame, or a source video.
- **Resolution against hardware:** the output size, the graphics memory it needs and the minutes per clip.
