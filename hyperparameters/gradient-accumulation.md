---
id: hyperparameter/gradient-accumulation
type: hyperparameter
slug: gradient-accumulation
schema_version: 2.2
title: "Gradient Accumulation"
name: gradient_accumulation_steps
context: training
priority: 11
importance: important
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "1 — 40"
controls: "How many small batches of examples a training run works through before it changes the model once: a way to get the effect of a large batch on a machine that can only hold a small one."
effects:
  decrease:
    - "Fewer small batches go into each update"
    - "Each update needs fewer passes through the model"
    - "The effective batch shrinks unless the per-device batch or the device count grows"
    - "At 1: the model is updated after every batch"
  increase:
    - "More small batches are averaged into each update"
    - "The effective batch grows with no extra memory"
    - "Each update needs that many passes through the model"
    - "Fewer updates for each pass over the data"
scale:
  min: 1
  max: 128
  typical_min: 1
  typical_max: 40
  spacing: log
typical_values:
  - setting: "Hugging Face Transformers: default of TrainingArguments"
    value: "1"
    source: "https://huggingface.co/docs/transformers/main_classes/trainer"
  - setting: "QLoRA training script: default, with a per-device batch of 1"
    value: "16"
    source: "https://github.com/artidoro/qlora/blob/main/qlora.py"
  - setting: "Alpaca-LoRA training script: default on one device (batch_size 128 divided by micro_batch_size 4)"
    value: "32"
    source: "https://github.com/tloen/alpaca-lora/blob/main/finetune.py"
  - setting: "nanoGPT training script: default on one process (written as 5 * 8), with batches of 12 sequences"
    value: "40"
    source: "https://github.com/karpathy/nanoGPT/blob/master/train.py"
mistakes:
  - mistake: "Raising the accumulation steps and treating the run as otherwise unchanged"
    why: >-
      Accumulation **multiplies the effective batch**: Hugging Face Transformers defines it as
      per-device batch × devices × accumulation steps. The batch size and the learning rate work
      as a pair: Goyal et al. kept the same accuracy from 256 to 8,192 images per batch only by
      scaling the learning rate together with the batch size.
    fix: >-
      Work out the effective batch before and after the change. If it changed, test the learning
      rate again. Goyal et al. give one tested starting point, shown for SGD on image
      classification: multiply the learning rate by the same factor as the batch.
  - mistake: "Reading logging_steps, eval_steps, save_steps or max_steps as a number of batches"
    why: >-
      The Hugging Face `Trainer` raises its step counter **once per optimizer update**, not once
      per batch. With 8 accumulation steps, `logging_steps=10` means every 80 batches, and
      `max_steps=1000` means 8,000 batches.
    fix: >-
      Count in updates. To keep an interval the same in batches, divide it by the accumulation
      steps when you raise them.
  - mistake: "In a hand-written loop, adding up the gradients without dividing the loss"
    why: >-
      PyTorch **adds** each new gradient to the stored one. Without a division, N accumulated
      batches give a gradient N times as large as the average, and the update is N times as big.
      The PyTorch example divides the loss by the number of accumulated batches before each
      backward pass; the `Trainer` and nanoGPT do the same.
    fix: >-
      Divide each batch's loss by the accumulation steps, and call `optimizer.step()` and
      `optimizer.zero_grad()` only after the last batch of the group.
  - mistake: "Expecting accumulation to make training faster"
    why: >-
      It saves **memory**, not time. An update with N accumulation steps still runs N forward
      and backward passes; they happen one after another instead of side by side.
    fix: >-
      Use it when the batch you want does not fit in memory. When it does fit, a larger
      per-device batch reaches the same effective batch in fewer passes.
tags: [training, memory, batch-size]
updated: 2026-11-23
related: [hyperparameter/effective-batch-size, hyperparameter/learning-rate, hyperparameter/training-duration, hyperparameter/gradient-clipping]
sources:
  - { url: "https://huggingface.co/docs/transformers/main_classes/trainer", title: "Hugging Face Transformers documentation: Trainer and TrainingArguments", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py", title: "Hugging Face Transformers source: training_args.py (defaults and descriptions)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/trainer.py", title: "Hugging Face Transformers source: trainer.py (the training loop)", trust: 0.9 }
  - { url: "https://docs.pytorch.org/docs/2.15/notes/amp_examples.html", title: "PyTorch documentation: Automatic Mixed Precision examples (Gradient accumulation)", trust: 0.9 }
  - { url: "https://docs.pytorch.org/docs/2.15/generated/torch.optim.Optimizer.zero_grad.html", title: "PyTorch documentation: torch.optim.Optimizer.zero_grad", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1706.02677", title: "Accurate, Large Minibatch SGD: Training ImageNet in 1 Hour (Goyal et al., 2017)", trust: 0.9 }
  - { url: "https://github.com/artidoro/qlora/blob/main/qlora.py", title: "QLoRA training script (qlora.py)", trust: 0.85 }
  - { url: "https://github.com/tloen/alpaca-lora/blob/main/finetune.py", title: "Alpaca-LoRA training script (finetune.py)", trust: 0.8 }
  - { url: "https://github.com/karpathy/nanoGPT/blob/master/train.py", title: "nanoGPT training script (train.py)", trust: 0.85 }
---

## Definition

**Gradient accumulation** means working through several small batches, keeping a running total of
what each one says about the model, and only then changing the model once.

- A **batch** is the group of training examples the model looks at together.
- The **gradient** is the result of looking at a batch: for every weight (adjustable number) in
  the model, which way and how strongly it should change to make the model less wrong.
- An **update**, also called an **optimizer step**, is the moment the weights are changed.
- A **pass** is one trip through the model: forward to get its answers, backward to get the
  gradient.

Without accumulation, every batch is followed by an update. With
`gradient_accumulation_steps = 4`, the gradients of four batches are combined first, and the
weights change once for all four.

The reason to do this is **memory**. A batch must fit in the memory of the graphics processing
unit (GPU) all at once. Accumulation lets a machine that fits only 4 examples behave, for the
purpose of each update, as if it had looked at 16 or 32.

**Analogy:** a poll that needs 32 answers, taken in a room that holds 4 people. You bring in 4
people at a time, eight times, keep a tally, and decide once at the end. The decision rests on
all 32 answers, and the room never held more than 4. The analogy shows the idea only; the exact
rule is under Calculation.

## How It Works

What happens in one update, as the Hugging Face `Trainer` does it:

1. Take the next small batch and run it **forward** through the model to get the loss (how wrong
   the model was).
2. Scale the loss down, so that the group as a whole gives an **average** and not a sum.
3. Run the **backward** pass. PyTorch adds the new gradient to the one already stored.
4. Repeat steps 1 to 3 until `gradient_accumulation_steps` batches are done.
5. Clip the accumulated gradient if gradient clipping is on, then call `optimizer.step()`: the
   weights change **once**.
6. Clear the stored gradient with `zero_grad()` and count **one** step.

What each value means, for a per-device batch of 4 on one device:

| Accumulation steps | Passes per update | Examples per update (effective batch) |
| --- | --- | --- |
| 1 | 1 | 4 |
| 2 | 2 | 8 |
| 4 | 4 | 16 |
| 8 | 8 | 32 |

What stays the same and what changes:

- **Memory stays the same.** Only one small batch is in memory at a time. The Hugging Face
  documentation describes the setting as simulating larger batch sizes without additional memory.
- **Work per update goes up.** An update now costs as many passes as there are accumulation
  steps.
- **Updates per pass over the data go down.** The same data is spread over fewer, larger updates.

Why the result matches a large batch:

- The gradient of a batch is the average of the gradients of its examples.
- Averaging four equal-sized batches and then averaging those four results gives the same number
  as averaging all the examples at once. Part A of the worked example shows it with numbers.
- This holds when each example's loss does not depend on the other examples in its batch. Goyal
  et al. point out one case where it does: **batch normalization** computes statistics over the
  batch, so a change of batch size changes the loss function itself.

Two details of the Hugging Face `Trainer`:

- **The last update of an epoch can be smaller.** If the number of batches does not divide evenly,
  the final group has fewer batches, and the loss is scaled by that smaller number.
- **With several devices,** the script may need a smaller value. Alpaca-LoRA and nanoGPT both
  divide their accumulation steps by the number of processes, so that the effective batch stays
  the same.

## Calculation

Three things are calculated: the **effective batch**, the **accumulation steps needed** for a
target, and the **number of updates in one pass over the data**.

### 1. The effective batch

In words: multiply the examples one device holds by the number of devices and by the number of
batches accumulated. This is the definition in the Hugging Face Transformers documentation; the
PyTorch documentation gives the same product.

$$
B_\text{eff} = b \times d \times a
$$

| Symbol | Meaning |
| --- | --- |
| $b$ | `per_device_train_batch_size`: examples in one batch on one device |
| $d$ | the number of devices (GPUs) training in parallel |
| $a$ | `gradient_accumulation_steps` |
| $B_\text{eff}$ | the effective batch: examples that go into one update |

### 2. The accumulation steps for a target

In words: divide the effective batch you want by what the devices hold together.

$$
a = \frac{B_\text{target}}{b \times d}
$$

Steps:

1. Find the largest per-device batch $b$ that fits in memory.
2. Multiply it by the number of devices $d$.
3. Divide the target effective batch by that product. If the result is not a whole number, pick
   a neighbouring whole number and accept a slightly different effective batch.

### 3. Updates in one epoch

An **epoch** is one full pass over the training data. In words: count the batches in an epoch,
divide by the accumulation steps, and round up. This is the formula in the `Trainer` source.

$$
U = \left\lceil \frac{n}{a} \right\rceil
$$

| Symbol | Meaning |
| --- | --- |
| $n$ | the number of batches in one epoch |
| $U$ | the number of updates in one epoch |
| $\lceil \; \rceil$ | "round up to the next whole number" |

## Worked Example

### Part A: two small batches give the same gradient as one large batch

Made-up numbers, small enough to do by hand: four examples, and for one weight their gradients
are **2, 4, 6 and 8**.

One batch of all four:

1. Add them up: 2 + 4 + 6 + 8 = 20.
2. Divide by 4: 20 / 4 = **5**.

Two batches of two, with 2 accumulation steps:

1. Batch 1 average: (2 + 4) / 2 = 3. Divide by the accumulation steps: 3 / 2 = 1.5.
2. Batch 2 average: (6 + 8) / 2 = 7. Divide by the accumulation steps: 7 / 2 = 3.5.
3. Add the two stored results: 1.5 + 3.5 = **5**.

**What this means:** the weight gets the same gradient, 5, either way. Leave out the division in
steps 1 and 2 and the total is 3 + 7 = 10, twice too large.

### Part B: the accumulation steps for a target

The defaults of the Alpaca-LoRA training script: a target batch of **128** examples and a
per-device batch of **4**.

1. One device: $a = 128 / (4 \times 1) = 32$.
2. Check: $4 \times 1 \times 32 = 128$.
3. Two devices: the script divides by the number of processes, $32 / 2 = 16$.
4. Check: $4 \times 2 \times 16 = 128$.

**What this means:** the effective batch is 128 in both cases. Each update costs 32 passes on
one device, or 16 passes on each of two devices.

### Part C: updates in one epoch

A made-up dataset of **1,000** examples, a per-device batch of 4 on one device, and 32
accumulation steps.

1. Batches in one epoch: 1,000 / 4 = 250.
2. Divide by the accumulation steps: 250 / 32 = 7.8125. Round up: **8 updates**.
3. Seven updates use 32 batches each: 7 × 32 = 224 batches.
4. The last update uses what is left: 250 − 224 = 26 batches.

**What this means:** one pass over 1,000 examples changes the model only 8 times. Without
accumulation it would change it 250 times, each time from 4 examples.

## What To Watch

- **The effective batch the trainer reports.** The Hugging Face `Trainer` logs "Total train batch
  size (w. parallel, distributed & accumulation)" and "Gradient Accumulation steps" at the start
  of a run. Check them against what you intended.
- **The meaning of a step.** The documentation notes that logging, evaluation and saving happen
  every `gradient_accumulation_steps × xxx_step` batches, because one step is one update.
- **The learning rate.** A changed effective batch is a changed pairing with the learning rate.
  See the effective batch size and learning rate pages.
- **Gradient clipping.** In the `Trainer`, clipping is applied once per update, to the
  accumulated gradient, not to each small batch.
- **Time per update.** It grows in proportion to the accumulation steps. A run that looks slow
  per step may be doing exactly what was asked.
- **Layers that look across the batch.** With batch normalization, small batches under
  accumulation are not the same computation as one large batch (Goyal et al.).
