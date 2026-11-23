---
id: hyperparameter/training-duration
type: hyperparameter
slug: training-duration
schema_version: 3.1
title: Training Duration
name: max_steps
context: training
priority: 3
importance: critical
goals:
- training
- finetuning
models:
- llm
- vision
- audio
- diffusion
- rl
range: 1,875 — 1,200,000 updates
controls: How long a training run lasts, counted either in updates of the model (steps) or in full passes over the training data (epochs).
effects:
  decrease:
  - The model is updated fewer times
  - The run costs less time and compute
  - The model sees less data, or sees each example fewer times
  - The learning-rate schedule is squeezed into fewer updates
  increase:
  - The model is updated more times
  - The run costs more time and compute
  - On a fixed dataset, every example is seen more often
  - Training error keeps falling, but error on held-out data can start to rise
  - The learning-rate schedule is stretched over more updates
scale:
  min: 100
  max: 10000000
  typical_min: 1875
  typical_max: 1200000
  spacing: log
typical_values:
- setting: QLoRA, fine-tuning LLaMA on the OASST1 dataset (batch of 16)
  value: 1,875
  source: https://arxiv.org/abs/2305.14314
- setting: QLoRA, fine-tuning LLaMA 7B and 13B on the HH-RLHF dataset (batch of 16)
  value: 10,000
  source: https://arxiv.org/abs/2305.14314
- setting: Original Transformer, base model, training from scratch (12 hours on 8 GPUs)
  value: 100,000
  source: https://arxiv.org/abs/1706.03762
- setting: Original Transformer, big model, training from scratch (3.5 days)
  value: 300,000
  source: https://arxiv.org/abs/1706.03762
- setting: BERT, training from scratch (about 40 epochs over its 3.3 billion word corpus)
  value: 1,000,000
  source: https://arxiv.org/abs/1810.04805
- setting: 'Llama 3 405B, training from scratch: length of its learning-rate schedule'
  value: 1,200,000
  source: https://arxiv.org/abs/2407.21783
mistakes:
- mistake: Setting max_steps and num_train_epochs together and expecting the epochs to count
  why: In Hugging Face Transformers a positive `max_steps` **overrides** `num_train_epochs`. The `Trainer` logs "max_steps is given, it will override any value given in num_train_epochs" and carries on.
  fix: Set one of the two. Leave `max_steps` at its default of -1 to train by epochs, or set it to a positive number to train by updates.
- mistake: Leaving max_steps unset with a streaming dataset
  why: 'A streaming dataset has no known length, so the number of updates in an epoch cannot be worked out. The `Trainer` raises a `ValueError`: the total number of steps must be known in advance to bound the training loop and to configure the learning-rate scheduler.'
  fix: Set `max_steps` to a positive number whenever the dataset cannot report its length.
- mistake: Changing the batch size or the accumulation steps and keeping the same max_steps
  why: A step is **one update**, and the data consumed by one update is the effective batch. The `Trainer` computes the examples seen as `max_steps` × total train batch size. Double the effective batch and the same `max_steps` reads twice as much data and costs about twice the compute.
  fix: Decide the length in examples (or epochs, or tokens) first, then divide by the effective batch to get the number of updates.
- mistake: Copying a number of epochs from a different setup
  why: Published values differ by more than a factor of ten. The BERT paper fine-tunes for 2, 3 or 4 epochs; the LoRA paper trains its RoBERTa-base adapters for 25 to 80 epochs depending on the task, and its GPT-3 adapters for 2.
  fix: Take the value from a source that matches your model, method and data size, and check it against the error on held-out data.
- mistake: Changing the run length without looking at the learning-rate schedule
  why: The `Trainer` builds the schedule from the total number of updates. A schedule that falls to zero does so at the **last** update, whatever that number is, so a longer or shorter run changes the learning rate at every point in between.
  fix: Look at the schedule again after changing the length. See the learning rate warmup and schedule page.
tags:
- training
- compute-budget
- epochs
updated: '2026-11-23'
related:
- hyperparameter/effective-batch-size
- hyperparameter/gradient-accumulation
- hyperparameter/lr-warmup-schedule
- hyperparameter/learning-rate
sources:
- url: https://huggingface.co/docs/transformers/main_classes/trainer
  title: 'Hugging Face Transformers documentation: Trainer and TrainingArguments'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py
  title: 'Hugging Face Transformers source: training_args.py (defaults and descriptions)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/trainer.py
  title: 'Hugging Face Transformers source: trainer.py (how the number of steps is worked out)'
  trust: 0.9
- url: https://www.deeplearningbook.org/contents/regularization.html
  title: 'Deep Learning (Goodfellow, Bengio, Courville), chapter 7: Regularization (section 7.8, Early Stopping)'
  trust: 0.9
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.9
- url: https://arxiv.org/abs/1810.04805
  title: 'BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)'
  trust: 0.9
- url: https://arxiv.org/abs/2302.13971
  title: 'LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)'
  trust: 0.9
- url: https://arxiv.org/abs/2407.21783
  title: The Llama 3 Herd of Models (2024)
  trust: 0.9
- url: https://arxiv.org/abs/2106.09685
  title: 'LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)'
  trust: 0.9
- url: https://arxiv.org/abs/2305.14314
  title: 'QLoRA: Efficient Finetuning of Quantized LLMs (Dettmers et al., 2023)'
  trust: 0.9
---

## Definition

**Training duration** is how long a training run goes on before it stops. It is counted in one
of three units:

- **Steps.** A **step** is one update: the model looks at a batch of examples and its weights
  (adjustable numbers) are changed once. In Hugging Face Transformers the setting is `max_steps`.
- **Epochs.** An **epoch** is one full pass over the training data, so that every example has
  been seen once. The setting is `num_train_epochs`.
- **Tokens.** Papers on large language models report the total number of **tokens** (words or
  pieces of words) the model was trained on.

The three describe the same run. A number of epochs becomes a number of steps once the size of
the dataset and the size of a batch are known; Calculation shows how.

**Analogy:** studying from a textbook. An epoch is reading the whole book once. A step is one
study session. Three epochs with short sessions means many sessions; the same three readings
with long sessions means fewer. The analogy shows how the units relate; it says nothing about
how much studying is enough.

## How It Works

**Two settings control the length in Hugging Face Transformers:**

| Setting | Default | Meaning |
| --- | --- | --- |
| `num_train_epochs` | 3.0 | Passes over the training data. A value such as 2.5 stops halfway through the third pass. |
| `max_steps` | -1 | Total number of updates. -1 means "not set". A positive value overrides `num_train_epochs`. |

**How the `Trainer` decides when to stop:**

1. If `max_steps` is positive, the run lasts exactly that many updates. If the data runs out
   first, the `Trainer` goes through it again until the number is reached.
2. Otherwise it counts the updates in one epoch and multiplies by `num_train_epochs`, rounding
   up.
3. If the dataset cannot report its length (a streaming dataset) and `max_steps` is not set, the
   run does not start: the `Trainer` raises an error.

**A step is an update, not a batch.** With gradient accumulation, several batches go into one
update, and only the update is counted.

**Why the length matters in both directions:**

- **Too short.** The model stops while it is still improving. The LLaMA authors report that
  their 7B model was still improving after 1 trillion tokens.
- **Too long, on a fixed dataset.** Goodfellow, Bengio and Courville describe what is often seen
  when a large model trains on a limited dataset: the **training error** keeps falling, but the
  error on a **validation set** (data held back and never trained on) begins to rise again.
- **The remedy they describe is early stopping:** keep a copy of the model each time the
  validation error improves, and return that copy at the end. In their words, the number of
  training steps is then "just another hyperparameter".

**Published fine-tuning runs, counted in epochs:**

| Run | Epochs |
| --- | --- |
| BERT, fine-tuning on a task | 2, 3 or 4 |
| LoRA on GPT-3 (all experiments) | 2 |
| LoRA on GPT-2 (E2E, WebNLG, DART) | 5 |
| LoRA on RoBERTa-base (GLUE tasks) | 25 to 80, depending on the task |

**Published training runs from scratch, counted in tokens:**

| Model | Tokens trained on |
| --- | --- |
| LLaMA, the 6.7 billion and 13.0 billion parameter models | 1.0 trillion |
| LLaMA, the 32.5 billion and 65.2 billion parameter models | 1.4 trillion |
| Llama 3 405B | 15.6 trillion |

The LLaMA authors set their choice against earlier advice: they write that Hoffmann et al.
recommend training a 10 billion parameter model on 200 billion tokens, while their own 7 billion
parameter model kept improving past 1 trillion.

## Calculation

Three things are calculated: the **updates in one epoch**, the **total number of updates**, and
the **amount of data a run reads**.

### 1. Updates in one epoch

In words: divide the number of training examples by the effective batch (the examples that go
into one update) and round up.

$$
U = \left\lceil \frac{N}{b \times d \times a} \right\rceil
$$

| Symbol | Meaning |
| --- | --- |
| $N$ | the number of examples in the training set |
| $b$ | `per_device_train_batch_size`: examples in one batch on one device |
| $d$ | the number of devices training in parallel |
| $a$ | `gradient_accumulation_steps`: batches combined before each update |
| $U$ | updates in one epoch |
| $\lceil \; \rceil$ | "round up to the next whole number" |

### 2. Total updates, from epochs

In words: multiply the epochs by the updates in one epoch and round up. This is the formula in
the `Trainer` source, used when `max_steps` is not set.

$$
S = \left\lceil E \times U \right\rceil
$$

| Symbol | Meaning |
| --- | --- |
| $E$ | `num_train_epochs` |
| $S$ | the total number of updates (steps) in the run |

### 3. Data read by a run, from steps

In words: multiply the updates by the effective batch to get examples; multiply again by the
sequence length to get tokens; divide the examples by the dataset size to get epochs.

$$
\text{examples} = S \times b \times d \times a
\qquad
\text{tokens} = \text{examples} \times L
\qquad
E = \frac{\text{examples}}{N}
$$

| Symbol | Meaning |
| --- | --- |
| $L$ | the length of one sequence, in tokens |

Steps, for planning a run:

1. Decide how much data the run should read: a number of epochs, examples or tokens.
2. Work out the effective batch $b \times d \times a$.
3. Convert with formula 2 or formula 3 to the unit your trainer needs.

## Worked Example

### Part A: from epochs to steps

A made-up training set of **1,000** examples, with the Hugging Face defaults: per-device batch
8, one device, no accumulation, 3 epochs.

1. Effective batch: $8 \times 1 \times 1 = 8$.
2. Updates in one epoch: $U = 1{,}000 / 8 = 125$.
3. Total updates: $S = 3 \times 125 = 375$.

**What this means:** "3 epochs" on this dataset is 375 updates. The learning-rate schedule is
laid out over those 375.

### Part B: from steps to epochs

The same dataset and batch, now with `max_steps = 500`.

1. Examples read: $500 \times 8 = 4{,}000$.
2. Epochs: $4{,}000 / 1{,}000 = 4$.

**What this means:** 500 steps is 4 passes over this dataset, and `num_train_epochs` is ignored.
With 4 accumulation steps the same 500 steps would read $500 \times 32 = 16{,}000$ examples: 16
passes.

### Part C: checking a published run

The numbers of the BERT paper: 1,000,000 steps, each with 256 sequences of 512 tokens, over a
corpus of 3.3 billion words.

1. Tokens per step: $256 \times 512 = 128{,}000$.
2. Tokens in the run: $1{,}000{,}000 \times 128{,}000 = 128{,}000{,}000{,}000$ (128 billion).
3. Passes over the corpus: $128 \text{ billion} / 3.3 \text{ billion} \approx 38.8$.

**What this means:** the result agrees with the paper's own statement of "approximately 40
epochs". The paper counts the corpus in words and the batch in tokens, so the match is rough.

### Part D: how many updates a token budget is

The numbers of the LLaMA paper for its largest model: 1.4 trillion tokens, in batches of 4
million tokens.

1. Updates: $1{,}400{,}000{,}000{,}000 / 4{,}000{,}000 = 350{,}000$.

**What this means:** the run is about 350,000 updates. This figure is computed here from the
two numbers the paper gives; the paper does not state a step count.

## What To Watch

- **Which setting is in force.** A positive `max_steps` wins over `num_train_epochs`. The
  `Trainer` logs "Num Epochs" and "Total optimization steps" at the start of a run.
- **Validation error next to training error.** Goodfellow et al. describe the pattern to look
  for on a fixed dataset: training error still falling while validation error has turned upward.
- **The quality of the data before the length of the run.** In the QLoRA authors' experiments,
  a larger dataset or more epochs improved their benchmark score only marginally (0.0 to 0.5
  points), while the difference between datasets was up to 40 times larger.
- **The effective batch.** Any change to the batch, the device count or the accumulation steps
  changes how much data a fixed number of steps reads.
- **The schedule.** Warmup and decay are laid out over the total number of updates, so they move
  when the length changes.
- **What each published value was used for.** The rows of the typical values table are runs on
  different models, data and hardware. A value copied to another setup is a starting point to
  test.
