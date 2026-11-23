---
id: hyperparameter/learning-rate
type: hyperparameter
slug: learning-rate
schema_version: 3.1
title: Learning Rate
name: learning_rate
context: training
priority: 1
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
range: 2e-5 — 1e-3
controls: 'How big a step a model takes each time it corrects itself during training: small steps learn slowly, large steps learn fast but can overshoot.'
effects:
  decrease:
  - Each update changes the model less
  - Training takes more updates to get anywhere
  - Too low, and training can stay stuck at a high error
  - When fine-tuning, the model keeps more of what it already knew
  increase:
  - Each update changes the model more
  - Training moves faster while the steps still fit the problem
  - Too high, and the error goes up instead of down
  - When fine-tuning, the model can lose what it already knew
scale:
  min: 1.0e-06
  max: 0.01
  typical_min: 2.0e-05
  typical_max: 0.001
  spacing: log
typical_values:
- setting: 'PyTorch: default of the AdamW and SGD optimizers'
  value: 1e-3
  source: https://docs.pytorch.org/docs/2.15/generated/torch.optim.AdamW.html
- setting: 'Hugging Face Transformers: default of TrainingArguments'
  value: 5e-5
  source: https://huggingface.co/docs/transformers/main_classes/trainer
- setting: 'Original Transformer, base model: highest point of its schedule'
  value: about 7e-4 (from the paper's formula with model width 512 and 4,000 warmup steps)
  source: https://arxiv.org/abs/1706.03762
- setting: BERT, training from scratch (Adam optimizer)
  value: 1e-4
  source: https://arxiv.org/abs/1810.04805
- setting: BERT, fine-tuning on a task (Adam optimizer)
  value: 5e-5, 3e-5 or 2e-5
  source: https://arxiv.org/abs/1810.04805
- setting: 'LLaMA, training from scratch: the 6.7 billion and 13.0 billion parameter models'
  value: '3.0e-4'
  source: https://arxiv.org/abs/2302.13971
- setting: 'LLaMA, training from scratch: the 32.5 billion and 65.2 billion parameter models'
  value: '1.5e-4'
  source: https://arxiv.org/abs/2302.13971
- setting: 'Llama 3 405B, training from scratch: highest point of its schedule'
  value: 8e-5
  source: https://arxiv.org/abs/2407.21783
- setting: LoRA adapters on GPT-2 (text generation tasks)
  value: 2e-4
  source: https://arxiv.org/abs/2106.09685
- setting: LoRA adapters on RoBERTa-base (GLUE tasks)
  value: 4e-4 or 5e-4, depending on the task
  source: https://arxiv.org/abs/2106.09685
mistakes:
- mistake: Fine-tuning with a learning rate sized for training from scratch
  why: 'A model that is already trained holds useful knowledge in its weights, and large steps overwrite it. Sun et al. fine-tuned BERT at four learning rates: at **4e-4** the training set **failed to converge**, and they report that a lower rate such as **2e-5** is needed to avoid **catastrophic forgetting** (the model losing what it learned before).'
  fix: Start from the fine-tuning values the model's authors report. For BERT the paper lists **5e-5, 3e-5 and 2e-5**.
- mistake: Changing the batch size and leaving the learning rate alone
  why: The batch size (how many examples are averaged into one update) and the learning rate work as a pair. Goyal et al. kept the same accuracy at batch sizes from 256 up to 8,192 images only by scaling the learning rate together with the batch size.
  fix: 'Apply their **linear scaling rule**: when the batch size is multiplied by k, multiply the learning rate by k. They showed it for image classification up to 8,192 images per batch and report that accuracy drops quickly beyond that.'
- mistake: Using the full learning rate from the very first update
  why: Goyal et al. found that large-batch training runs into optimization difficulties early on, while the network is changing rapidly. The Transformer, BERT, LLaMA and Llama 3 papers all begin with a warmup. In Hugging Face Transformers the default is `warmup_steps=0`, so there is **no warmup unless you set one**.
  fix: 'Add a **warmup**: raise the learning rate gradually from a small value to the full value over the first updates (4,000 in the Transformer paper, 10,000 for BERT, 2,000 for LLaMA).'
- mistake: Searching for a good value in small, evenly spaced steps
  why: 'Useful learning rates span factors of ten, not small differences: the values in the table above run from 2e-5 to 1e-3. Trying 1e-4, 1.1e-4 and 1.2e-4 tests almost the same thing three times.'
  fix: Search on a **logarithmic scale**, where each candidate is a multiple of the last. Goodfellow et al. give the example set 0.1, 0.01, 1e-3, 1e-4, 1e-5.
visual:
  kind: schedule
  peak: 5.0e-05
  warmup_steps: 100
  total_steps: 1000
  curves:
  - constant
  - linear
  - cosine
  marks:
  - 50
  - 100
  - 325
  - 550
  - 1000
  caption: The learning rate at every update of a 1,000-update run, for three schedules of Hugging Face Transformers. Each rises in a straight line to 5e-5 (the library's default) over the first 100 updates. The run length and the warmup length are small round numbers picked for this figure. The marked updates are the ones the worked example computes.
tags:
- training
- optimization
- learning-rate
updated: '2026-11-22'
related:
- hyperparameter/lr-warmup-schedule
- hyperparameter/effective-batch-size
sources:
- url: https://www.deeplearningbook.org/contents/guidelines.html
  title: 'Deep Learning (Goodfellow, Bengio, Courville), chapter 11: Practical Methodology'
  trust: 0.9
- url: https://docs.pytorch.org/docs/2.15/generated/torch.optim.SGD.html
  title: 'PyTorch documentation: torch.optim.SGD'
  trust: 0.9
- url: https://docs.pytorch.org/docs/2.15/generated/torch.optim.AdamW.html
  title: 'PyTorch documentation: torch.optim.AdamW'
  trust: 0.9
- url: https://huggingface.co/docs/transformers/main_classes/trainer
  title: 'Hugging Face Transformers documentation: Trainer and TrainingArguments'
  trust: 0.9
- url: https://huggingface.co/docs/transformers/main_classes/optimizer_schedules
  title: 'Hugging Face Transformers documentation: Optimization (learning rate schedules)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/optimization.py
  title: 'Hugging Face Transformers source: optimization.py (schedule formulas)'
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
- url: https://arxiv.org/abs/1706.02677
  title: 'Accurate, Large Minibatch SGD: Training ImageNet in 1 Hour (Goyal et al., 2017)'
  trust: 0.9
- url: https://arxiv.org/abs/1905.05583
  title: How to Fine-Tune BERT for Text Classification? (Sun et al., 2019)
  trust: 0.9
---

## Definition

The **learning rate** is the number that sets how far a model's weights move in one update.

- **Weights** are the numbers inside a model that training adjusts. A large language model has
  billions of them.
- An **update** is one correction: the model looks at some examples, measures how wrong it was
  (the **loss**), and nudges every weight in the direction that would have made it less wrong.
- That direction, together with how steeply the loss changes, is called the **gradient**.
- The learning rate **multiplies the gradient**. The same gradient moves the weights ten times
  as far at 1e-3 as at 1e-4.

Values are written in scientific notation: `1e-4` means 0.0001 and `5e-5` means 0.00005.

**Analogy:** walking downhill in fog. The gradient tells you which way is down from where you
stand; the learning rate is the length of your stride. Short strides are safe and slow. Long
strides are quick, until one carries you across the valley and up the other side. The analogy
shows the trade-off only; the exact rule is under Calculation.

Goodfellow, Bengio and Courville call the learning rate "perhaps the most important
hyperparameter" and advise that if you have time to tune only one, tune this one.

## How It Works

**The learning rate is usually not one fixed number.** A training run follows a **schedule**: a
rule that gives the learning rate for each update. The value you set (`learning_rate`) is the
highest point of that schedule.

How to read the figure:

- **Left to right** is the training run, from the first update to the last.
- **Height** is the learning rate used at that update.
- All three lines **climb together** for the first 100 updates. That climb is the **warmup**.
- After the warmup the lines part: one **stays flat**, one falls in a **straight line** to zero,
  one falls along a **cosine curve** (slowly at first, fastest in the middle, slowly at the end).

Why a run starts with a warmup:

- Goyal et al. found that training with large batches runs into optimization difficulties early
  on, while the network is changing rapidly.
- Their remedy is a **gradual warmup**: start from a small learning rate and raise it a little at
  every update until it reaches the full value.
- The Hugging Face documentation gives the reason in one line: warmup helps stabilize training in
  the initial phase.

Why the learning rate then comes down:

- Every model in the table below lowers it after the warmup.
- Part A of the worked example shows the mechanism: a step that is too large for the distance
  still to go jumps past the best value instead of landing on it.

What published models did:

| Model | Warmup | After the warmup |
| --- | --- | --- |
| Original Transformer (2017) | 4,000 updates, straight-line rise | Falls in proportion to 1 / √(update number) |
| BERT | 10,000 updates | Straight-line fall, over 1,000,000 updates in total |
| LLaMA | 2,000 updates | Cosine fall, ending at 10% of the highest value |
| Llama 3 405B | 8,000 updates, straight-line rise | Cosine fall to 8e-7, over 1,200,000 updates |

In Hugging Face Transformers the schedule is chosen with `lr_scheduler_type`. Its default is
`"linear"` (the straight-line fall); `"cosine"` and `"constant_with_warmup"` are the other two
lines of the figure.

## Calculation

Two things are calculated: **what one update does**, and **what the learning rate is at a given
update** of a schedule. A third rule covers a change of batch size.

### 1. One update

In words: take the current weight, and subtract the gradient multiplied by the learning rate.
This is the plain form of stochastic gradient descent (SGD) as the PyTorch documentation writes
it.

$$
\theta_t = \theta_{t-1} - \gamma \cdot g_t
$$

| Symbol | Meaning |
| --- | --- |
| $\theta_{t-1}$ | the weight before the update |
| $\theta_t$ | the weight after the update |
| $g_t$ | the gradient: how much the loss rises per unit increase of the weight |
| $\gamma$ | the learning rate |

Steps:

1. Compute the gradient $g_t$ for the current weight.
2. Multiply it by the learning rate $\gamma$. This is the size of the step.
3. Subtract the step from the weight.

This is the rule for plain SGD. Other optimizers, such as Adam and AdamW, build the step from the
gradient in their own way; their PyTorch pages give the exact rule.

### 2. The learning rate at update s

In words: during the warmup the learning rate is a growing share of the highest value; after the
warmup it is a shrinking share. The share is called the **multiplier**, a number between 0 and 1.

$$
\text{lr}(s) = \text{peak} \times m(s)
$$

| Symbol | Meaning |
| --- | --- |
| $s$ | the number of the current update, counted from 0 |
| $W$ | the number of warmup updates |
| $N$ | the total number of updates in the run |
| $\text{peak}$ | the highest learning rate (the `learning_rate` you set) |
| $m(s)$ | the multiplier at update $s$ |
| $p$ | the share of the after-warmup part of the run that is done, from 0 to 1 |

Steps, using the formulas of the Hugging Face Transformers schedules:

1. **If $s < W$ (still warming up):** $m(s) = \dfrac{s}{W}$. Skip to step 4.
2. **Otherwise, compute the progress** through the rest of the run: $p = \dfrac{s - W}{N - W}$.
3. **Turn the progress into a multiplier**, depending on the schedule:
   - constant with warmup: $m(s) = 1$
   - linear: $m(s) = 1 - p$, which is the same as $\dfrac{N - s}{N - W}$
   - cosine: $m(s) = \dfrac{1}{2}\,\bigl(1 + \cos(\pi \cdot p)\bigr)$
4. **Multiply** the highest learning rate by the multiplier.

In the cosine formula the angle $\pi \cdot p$ is in radians; it equals $180^\circ \times p$.

### 3. A change of batch size

In words: if each update averages over k times as many examples, take a step k times as large.
This is the **linear scaling rule** of Goyal et al., stated for SGD on image classification.

$$
\text{lr}_\text{new} = k \times \text{lr}_\text{old}
$$

| Symbol | Meaning |
| --- | --- |
| $k$ | the new batch size divided by the old batch size |
| $\text{lr}_\text{old}$ | the learning rate used with the old batch size |
| $\text{lr}_\text{new}$ | the learning rate to use with the new batch size |

## Worked Example

### Part A: one update, at four learning rates

A made-up problem small enough to do by hand: one weight $w$, and a loss equal to $w^2$. The loss
is smallest (zero) at $w = 0$, so that is where training should end up. For this loss the
gradient is $2w$.

Start at $w = 1$. The loss is $1^2 = 1$ and the gradient is $2 \times 1 = 2$.

| Learning rate | Step = learning rate × gradient | New weight = 1 − step | New loss = weight² | Result |
| --- | --- | --- | --- | --- |
| 0.1 | 0.1 × 2 = 0.2 | 1 − 0.2 = 0.8 | 0.8² = 0.64 | Better, slowly |
| 0.5 | 0.5 × 2 = 1.0 | 1 − 1.0 = 0 | 0² = 0 | Lands on the best value |
| 1.0 | 1.0 × 2 = 2.0 | 1 − 2.0 = −1 | (−1)² = 1 | Jumps across; no better than before |
| 1.5 | 1.5 × 2 = 3.0 | 1 − 3.0 = −2 | (−2)² = 4 | Worse than before |

**What this means:** the same gradient gives progress, a perfect step, no progress or damage,
depending only on the learning rate. The useful values here are specific to this toy loss; a real
model has its own, far smaller ones.

### Part B: the learning rate at five updates of a schedule

The numbers of the figure above: highest value $\text{peak}$ = 5e-5 (the default of Hugging Face
Transformers), warmup $W$ = 100 updates, run length $N$ = 1,000 updates. The two step counts are
small round numbers chosen for this example.

1. **Update 50** (warming up, since 50 < 100): $m = 50 / 100 = 0.5$.
   Learning rate = 5e-5 × 0.5 = **2.5e-5**, on every schedule.
2. **Update 100** (the warmup has just ended): $p = (100 - 100) / (1000 - 100) = 0$.
   - linear: $m = 1 - 0 = 1$
   - cosine: $m = \frac{1}{2}(1 + \cos 0^\circ) = \frac{1}{2}(1 + 1) = 1$
   - Learning rate = 5e-5 × 1 = **5e-5**, the highest point, on every schedule.
3. **Update 325:** $p = (325 - 100) / 900 = 225 / 900 = 0.25$.
   - linear: $m = 1 - 0.25 = 0.75$, so 5e-5 × 0.75 = **3.75e-5**
   - cosine: the angle is 180° × 0.25 = 45°, and cos 45° ≈ 0.7071.
     $m = \frac{1}{2}(1 + 0.7071) = 0.8536$, so 5e-5 × 0.8536 ≈ **4.27e-5**
4. **Update 550:** $p = (550 - 100) / 900 = 450 / 900 = 0.5$.
   - linear: $m = 1 - 0.5 = 0.5$, so 5e-5 × 0.5 = **2.5e-5**
   - cosine: the angle is 180° × 0.5 = 90°, and cos 90° = 0.
     $m = \frac{1}{2}(1 + 0) = 0.5$, so 5e-5 × 0.5 = **2.5e-5**
5. **Update 1,000** (the end): $p = 900 / 900 = 1$.
   - linear: $m = 1 - 1 = 0$
   - cosine: cos 180° = −1, so $m = \frac{1}{2}(1 - 1) = 0$
   - Learning rate = **0** on both. The constant schedule is still at 5e-5.

**What this means:** the two falling schedules start and end at the same values and cross at
the halfway point; in between, the cosine schedule keeps the learning rate higher for the first
half of the fall (4.27e-5 against 3.75e-5 at update 325) and lower for the second half.

### Part C: a larger batch

The numbers Goyal et al. used: a learning rate of **0.1** for a batch of **256** images.

1. New batch size: 1,024 images. $k = 1024 / 256 = 4$.
2. New learning rate: $4 \times 0.1 = 0.4$.

**What this means:** four times the batch, four times the learning rate. The authors showed that
this holds up to 8,192 images per batch (k = 32, learning rate 3.2) and paired it with a warmup.

## What To Watch

- **The training loss, update by update.** Goodfellow et al. describe a U-shaped relation between
  learning rate and training error:
  - **Too high:** the error can go **up** instead of down.
  - **Too low:** training is slower and can stay **stuck** at a high error.
- **The learning rate that is actually applied.** With a schedule, the value changes at every
  update. Log it and compare it with the curve you expect: a flat line where you wanted a warmup
  means the warmup was never set.
- **The defaults you did not choose.** Hugging Face `TrainingArguments` uses a learning rate of
  5e-5, the linear schedule and no warmup unless you say otherwise. PyTorch's AdamW and SGD
  default to 1e-3.
- **What the value was tuned for.** Each row of the typical values table belongs to one model,
  one optimizer and one batch size. A value copied to a different setup is a starting point to
  test, not a result.
