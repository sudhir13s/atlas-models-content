---
id: model/flux-3-action-base
type: model
slug: flux-3-action-base
schema_version: 1.1
title: flux-3-action-base
organization: organization/black-forest-labs
release_date: '2026-09-22'
release_year: 2026
release_month: 9
license_note: flux-kommunity-license
model_category: robotics-and-control
tasks:
- task/robotics
summary: FLUX 3 Action Base is part of FLUX 3 Action, an open-weights 7-billion-parameter world action model from Black Forest Labs. Such a model takes camera frames, the robot's state and a text instruction, and returns the next chunk of actions, denoised together with the next video frames. This repository holds the action base weights and shared encoders used to adapt it to a new robot, and the card states it is not a complete robot policy.
intended_use: The card describes the weights as an adaptation component for training action heads for a new robot, simulator or game. It names as out of scope any use that breaks the law, controls a machine in a way that endangers people without human oversight and a means to stop it, or supports fully automated decisions with high impact on individuals.
what_sets_it_apart: The card says the base has video and text streams, a shared action trunk, and ee50 and gaming action heads, and that new embodiments need their own action heads. The shared text encoder is an unmodified copy of Qwen3-VL-4B-Instruct.
running_it: The card says the model outputs joint targets and nothing in it limits joint velocity, force or workspace, so the application must enforce those limits and keep a hardware stop within reach. It advises validating in a simulator or with the arm's safety limits engaged before running near people.
filters:
  is_gated: true
  pipeline_tag: robotics
  library: lerobot
hub_signals:
  downloads_30d: 1045
  likes: 172
  last_modified: '2026-11-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/black-forest-labs/flux-3-action-base
aliases:
- black-forest-labs/flux-3-action-base
lineage:
  parents: []
  children: []
quotes:
  summary: FLUX 3 Action is an open weights 7B world action model.
  intended_use: This is an adaptation component, not a complete robot policy.
  what_sets_it_apart: The text encoder is an unmodified copy of Qwen/Qwen3-VL-4B-Instruct;
  running_it: the application must enforce those limits and keep a hardware stop within reach.
tags:
- robotics
updated: '2026-11-23'
sources:
- url: https://huggingface.co/black-forest-labs/flux-3-action-base
  title: black-forest-labs/flux-3-action-base — model page (Hugging Face)
  trust: 0.9
---

## Summary

FLUX 3 Action Base is part of FLUX 3 Action, an open-weights 7-billion-parameter world action model from Black Forest Labs. Such a model takes camera frames, the robot's state and a text instruction, and returns the next chunk of actions, denoised together with the next video frames. This repository holds the action base weights and shared encoders used to adapt it to a new robot, and the card states it is not a complete robot policy.

## Intended Use

The card describes the weights as an adaptation component for training action heads for a new robot, simulator or game. It names as out of scope any use that breaks the law, controls a machine in a way that endangers people without human oversight and a means to stop it, or supports fully automated decisions with high impact on individuals.

## What Sets It Apart

The card says the base has video and text streams, a shared action trunk, and ee50 and gaming action heads, and that new embodiments need their own action heads. The shared text encoder is an unmodified copy of Qwen3-VL-4B-Instruct.

## Running It

The card says the model outputs joint targets and nothing in it limits joint velocity, force or workspace, so the application must enforce those limits and keep a hardware stop within reach. It advises validating in a simulator or with the arm's safety limits engaged before running near people.
