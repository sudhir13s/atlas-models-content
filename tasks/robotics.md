---
id: task/robotics
type: task
slug: robotics
schema_version: 1.0
title: "Robotics"
group: task-group/robotics-and-control
order: 1
summary: "Turn camera images and a written instruction into the motor commands that move a robot."
hf_pipeline_tags: [robotics]
inputs: [image, text]
outputs: [action]
tags: [robotics, manipulation, vision-language-action]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/models?pipeline_tag=robotics", title: "Hugging Face — models tagged robotics", trust: 0.9 }
---

## What It Is
A robotics model, or policy, **decides the next movement of a robot from what it sees and is told**.

- **Vision-language-action (VLA) policies:** one network reads camera frames and an instruction, then emits actions.
- **Manipulation focus:** most published models control arms and grippers that pick, place and fold.
- **Learning from demonstrations:** policies are trained on recordings of people operating the robot.

## When To Use
- **Use it** when a physical robot must carry out varied tasks described in plain language.
- **Use reinforcement learning instead** when one fixed task can be practised in simulation by trial and error.
- **Use object detection or depth estimation instead** when you only need perception and plan motion yourself.

## How To Choose
- **Robot compatibility:** the arms, grippers and camera layout the policy was trained on.
- **Action format and rate:** joint positions or gripper poses, and how many actions per second it produces.
- **Fine-tuning data needed:** the number of demonstrations required to adapt it to a new task.
- **Evaluation setting:** results in simulation do not guarantee the same success on a real robot.
