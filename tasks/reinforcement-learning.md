---
id: task/reinforcement-learning
type: task
slug: reinforcement-learning
schema_version: 3.1
title: Reinforcement Learning
group: task-group/robotics-and-control
order: 2
summary: Choose actions in an environment so as to collect the most reward over time.
hf_pipeline_tags:
- reinforcement-learning
inputs:
- tabular
outputs:
- action
tags:
- reinforcement-learning
- agents
- control
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/reinforcement-learning
  title: Hugging Face — Reinforcement Learning task
  trust: 0.9
---

## What It Is

A reinforcement learning (RL) agent **learns by trial and error which action to take in each situation**.

- **Observations in, actions out:** the input is the environment's current state, such as positions and speeds.
- **Reward as the only teacher:** the agent is scored on outcomes and is never shown correct answers.
- **One environment per agent:** a published agent is trained for a single game, simulator or control problem.

## When To Use

- **Use it** when a task can be simulated cheaply and success can be scored automatically.
- **Use robotics instead** when a real robot must follow varied instructions learned from demonstrations.
- **Use tabular classification instead** when each decision stands alone and labelled examples exist.

## How To Choose

- **Environment match:** an agent only works in the environment, and version, it was trained on.
- **Algorithm and action type:** the training method, and whether actions are discrete choices or continuous values.
- **Reported return:** the average reward and its spread across evaluation episodes.
- **Training library:** the framework needed to load, run and continue training the agent.
