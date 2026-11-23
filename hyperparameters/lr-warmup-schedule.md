---
id: hyperparameter/lr-warmup-schedule
type: hyperparameter
slug: lr-warmup-schedule
schema_version: 2.2
title: "Learning Rate Warmup and Schedule"
name: warmup_steps
context: training
priority: 4
importance: critical
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "0 — 30,000 updates"
controls: "How many updates the learning rate takes to climb from zero to its highest value at the start of training (the warmup), and the shape of its fall after that (the schedule)."
effects:
  decrease:
    - "The learning rate reaches its peak after fewer updates"
    - "The first updates are larger"
    - "At 0 the first update already uses the full learning rate (the Hugging Face default)"
    - "Goyal et al.: with no warmup, a batch of 8,192 images trained to a higher error"
  increase:
    - "The learning rate takes more updates to reach its peak"
    - "The first updates are smaller"
    - "In a run of fixed length, the fall starts later and is steeper"
    - "Longer than the run itself, and the peak is never reached"
scale:
  min: 0
  max: 40000
  typical_min: 0
  typical_max: 30000
  spacing: linear
typical_values:
  - setting: "Hugging Face Transformers: default of TrainingArguments (no warmup)"
    value: "0"
    source: "https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py"
  - setting: "LLaMA, training from scratch, then a cosine fall to 10% of the peak"
    value: "2,000"
    source: "https://arxiv.org/abs/2302.13971"
  - setting: "Original Transformer, of 100,000 updates for the base model"
    value: "4,000"
    source: "https://arxiv.org/abs/1706.03762"
  - setting: "Llama 3 405B, of 1,200,000 updates, then a cosine fall to 8e-7"
    value: "8,000"
    source: "https://arxiv.org/abs/2407.21783"
  - setting: "BERT, training from scratch, of 1,000,000 updates, then a straight-line fall"
    value: "10,000"
    source: "https://arxiv.org/abs/1810.04805"
  - setting: "RoBERTa-large, training from scratch, of 500,000 updates, then a straight-line fall"
    value: "30,000"
    source: "https://arxiv.org/abs/1907.11692"
mistakes:
  - mistake: "Relying on the defaults and getting no warmup"
    why: >-
      Hugging Face `TrainingArguments` defaults to `warmup_steps=0` and `lr_scheduler_type="linear"`:
      the run starts at the **full learning rate** and falls in a straight line to zero. Every model
      in the table on this page used a warmup.
    fix: >-
      Set `warmup_steps` and `lr_scheduler_type` explicitly, taking both from the recipe you follow.
  - mistake: "Writing 1.0 to mean the whole run, or using warmup_ratio on a current version"
    why: >-
      In the current Transformers source, `warmup_steps` is read as a **share of the run only when
      it is below 1**; from 1 up it is a **count of updates**, so `warmup_steps=1.0` means a single
      warmup update. Version 4.57 had a separate `warmup_ratio` argument; the current
      `TrainingArguments` has none.
    fix: >-
      Write a share as a number below 1 (`warmup_steps=0.06` for 6%) and a count as a whole number
      (`warmup_steps=500`). Check which arguments your installed version accepts.
  - mistake: "Copying a warmup length from a run of a very different length"
    why: >-
      A count of updates means something only next to the length of its run: BERT's 10,000 warmup
      updates are **1%** of its 1,000,000. In a fine-tuning run of 2,000 updates the same 10,000 would
      be longer than the run, and in the Hugging Face schedules the learning rate would still be
      rising when training stops.
    fix: >-
      Convert the source's warmup into a **share** of its run (the table under How It Works gives
      them) and apply that share to your run, or use the share form of `warmup_steps`. RoBERTa
      fine-tuned with a warmup of 6%.
  - mistake: "Choosing \"cosine\" and expecting the learning rate to stop at a floor"
    why: >-
      In Hugging Face, `"cosine"` falls **all the way to zero**. LLaMA and GPT-3 stopped their cosine
      fall at **10%** of the peak, and Llama 3 405B at 8e-7, one hundredth of its 8e-5 peak.
    fix: >-
      Use `lr_scheduler_type="cosine_with_min_lr"` with `lr_scheduler_kwargs={"min_lr_rate": 0.1}`
      for a floor at 10% of the peak, as in the figure.
  - mistake: "Warming up with a low constant learning rate instead of a gradual rise"
    why: >-
      Goyal et al. compared both at a batch of 8,192 images. A low constant rate for the first epochs
      "is not sufficient", and switching from it to the full rate can make the training error jump:
      the final validation error was **25.88%**, against **23.74%** with a gradual warmup and 24.84% with none.
    fix: >-
      Use a **gradual** (straight-line) warmup from zero, which is what every Hugging Face schedule
      with a warmup does.
visual:
  kind: schedule
  peak: 0.0003
  warmup_steps: 200
  total_steps: 2000
  final_ratio: 0.1
  curves: [linear, cosine, inverse_sqrt]
  marks: [100, 200, 650, 1100, 2000]
  caption: "The learning rate at every update of a 2,000-update run, for three schedules of Hugging Face Transformers. Each rises in a straight line to 3e-4 (the peak LLaMA reports for its 6.7 billion parameter model) over the first 200 updates. The cosine curve stops at 10% of the peak, as LLaMA's did (\"cosine_with_min_lr\" with min_lr_rate 0.1). The run length and the warmup length are example values chosen so the warmup is visible. The marked updates are the ones the worked example computes."
tags: [training, learning-rate, scheduler, warmup]
updated: 2026-11-23
related: [hyperparameter/learning-rate, hyperparameter/effective-batch-size, hyperparameter/training-duration]
sources:
  - { url: "https://arxiv.org/abs/1706.02677", title: "Accurate, Large Minibatch SGD: Training ImageNet in 1 Hour (Goyal et al., 2017)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1608.03983", title: "SGDR: Stochastic Gradient Descent with Warm Restarts (Loshchilov and Hutter, 2017)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/optimization.py", title: "Hugging Face Transformers source: optimization.py (schedule formulas)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py", title: "Hugging Face Transformers source: training_args.py (warmup_steps and lr_scheduler_type)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/v4.57.0/src/transformers/training_args.py", title: "Hugging Face Transformers v4.57.0 source: training_args.py (warmup_ratio)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/trainer_utils.py", title: "Hugging Face Transformers source: trainer_utils.py (SchedulerType names)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1810.04805", title: "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1907.11692", title: "RoBERTa: A Robustly Optimized BERT Pretraining Approach (Liu et al., 2019)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2005.14165", title: "Language Models are Few-Shot Learners (Brown et al., 2020), the GPT-3 paper", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2302.13971", title: "LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models (2024)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2010.11929", title: "An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale (Dosovitskiy et al., 2020), the Vision Transformer paper", trust: 0.9 }
---

## Definition

A **learning rate schedule** is the rule that gives the learning rate at every update of a
training run. The **warmup** is its first part, where the learning rate climbs from zero to its
highest value. `warmup_steps` sets how long that climb is.

- The **learning rate** is the number that sets how far the model's weights move at each update
  (see the Learning Rate page).
- An **update** (or **step**) is one correction of the weights, made after looking at one batch of
  examples.
- The value set as `learning_rate` is the **peak**: the highest point of the schedule.
- During the **warmup** the learning rate rises from 0 to the peak, in a straight line.
- After the warmup the schedule decides the **fall**: none (constant), a straight line, a cosine
  curve, or a curve proportional to 1 / √(update number).

**Analogy:** pulling away in a car. You do not start in top gear; you build up speed, cruise, and
slow down as you approach where you are going. The analogy shows the shape only; the reasons
measured in the sources are under How It Works.

## How It Works

**How to read the figure:**

- **Left to right** is the run: 2,000 updates.
- **Height** is the learning rate at that update.
- All three lines **climb together** for the first 200 updates: the warmup.
- Then they part:
  - **linear** falls in a straight line to zero at the last update;
  - **cosine** falls slowly at first, fastest in the middle, slowly at the end, and here stops at
    10% of the peak;
  - **inverse square root** falls quickly just after the warmup and then ever more slowly. It never
    reaches zero and does not depend on the run length.

This page uses a different peak, run length and set of schedules from the Learning Rate page, whose
figure shows the constant, linear and cosine schedules ending at zero.

**Why a run starts with a warmup.** Goyal et al. (2017) trained an image classifier (ResNet-50 on
ImageNet) with very large batches:

- The rule they used to set the learning rate for a large batch "breaks down when the network is
  changing rapidly", which "commonly occurs in early stages of training".
- Their remedy: a **gradual warmup** that starts small and adds a constant amount at every update
  until the full learning rate is reached after 5 epochs (5 passes over the data).
- Results at a batch of 8,192 images (top-1 error, lower is better):

| Warmup | Error |
| --- | --- |
| Batch of 256, the reference run | 23.60% |
| None | 24.84% |
| Constant: a low fixed rate first | 25.88% |
| Gradual | 23.74% |

The Hugging Face documentation sums the purpose up in one line: warmup "helps stabilize training
in the initial phase".

**Where the cosine curve comes from.** Loshchilov and Hutter (SGDR, 2017) introduced the cosine
fall: the learning rate starts at its maximum and, after a set number of epochs, reaches its
minimum, following half a cosine wave between the two. (SGDR also restarts the curve
periodically; the schedules on this page fall once.)

**Where the inverse square root comes from.** The original Transformer paper raised the learning
rate in a straight line for 4,000 updates and then lowered it "proportionally to the inverse square
root of the step number". Hugging Face's `"inverse_sqrt"` schedule gives the same curve when its
time scale equals the warmup length, which is its default.

**What published models did:**

| Model | Warmup | Run length | Warmup share | After the warmup |
| --- | --- | --- | --- | --- |
| Original Transformer, base | 4,000 updates | 100,000 | 4% | ∝ 1 / √(update) |
| BERT | 10,000 updates | 1,000,000 | 1% | Straight line |
| RoBERTa-base | 24,000 updates | 500,000 | 4.8% | Straight line |
| RoBERTa-large | 30,000 updates | 500,000 | 6% | Straight line |
| RoBERTa, fine-tuning | 6% of the run | | 6% | Straight line |
| Vision Transformer | 10,000 updates | | | Straight line (cosine on ImageNet) |
| GPT-3 | 375 million tokens | 260 billion tokens of fall | | Cosine to 10% of the peak |
| LLaMA | 2,000 updates | | | Cosine to 10% of the peak |
| Llama 3 405B | 8,000 updates | 1,200,000 | 0.67% | Cosine to 8e-7 (1% of 8e-5) |

Shares are the warmup divided by the run length, where the paper gives both. GPT-3 counts its
warmup and fall in tokens (pieces of text processed), not updates.

**In Hugging Face Transformers:**

- `warmup_steps` (default **0**): a whole number from 1 up is a count of updates; a number below 1
  is a share of the run, rounded up to whole updates.
- `lr_scheduler_type` (default **"linear"**) names the fall: `"linear"`, `"cosine"`,
  `"cosine_with_min_lr"`, `"inverse_sqrt"`, `"constant_with_warmup"` and others.
- `lr_scheduler_kwargs` passes extra settings, such as `{"min_lr_rate": 0.1}` for the floor of
  `"cosine_with_min_lr"`.

## Calculation

Two things are calculated: **the warmup length** from a share, and **the learning rate at
update s**.

### 1. The warmup length from a share (Hugging Face)

In words: multiply the total number of updates by the share and round up.

$$
W = \lceil N \times r \rceil
$$

| Symbol | Meaning |
| --- | --- |
| $N$ | the total number of updates in the run |
| $r$ | the share, the value of `warmup_steps` when it is below 1 |
| $W$ | the number of warmup updates |
| $\lceil \cdot \rceil$ | round up to the next whole number |

### 2. The learning rate at update s

In words: during the warmup the learning rate is a growing share of the peak; after it, each
schedule turns the progress through the run into a shrinking share. That share is the
**multiplier**.

$$
\text{lr}(s) = \text{peak} \times m(s)
$$

| Symbol | Meaning |
| --- | --- |
| $s$ | the number of the current update, counted from 0 |
| $\text{peak}$ | the highest learning rate (`learning_rate`) |
| $m(s)$ | the multiplier at update $s$, between 0 and 1 |
| $p$ | the share of the after-warmup part that is done: $p = \dfrac{s - W}{N - W}$ |
| $f$ | the floor of the cosine curve as a share of the peak (`min_lr_rate`) |

Steps, using the formulas of the Hugging Face Transformers source:

1. **If $s < W$ (warming up):** $m(s) = \dfrac{s}{W}$. Skip to step 4.
2. **Compute the progress** $p = \dfrac{s - W}{N - W}$ (the inverse square root does not need it).
3. **Turn it into a multiplier:**
   - linear: $m(s) = 1 - p$
   - cosine with a floor: $m(s) = f + (1 - f) \times \dfrac{1}{2}\bigl(1 + \cos(\pi \cdot p)\bigr)$;
     with $f = 0$ this is the plain `"cosine"`
   - inverse square root: $m(s) = \dfrac{1}{\sqrt{s / W}} = \sqrt{\dfrac{W}{s}}$
4. **Multiply** the peak by the multiplier.

The angle $\pi \cdot p$ is in radians; it equals $180^\circ \times p$.

**The original Transformer formula** is the inverse square root schedule written in one line, with
the model width $d$ and steps counted from 1:

$$
\text{lr}(s) = d^{-0.5} \cdot \min\!\left(s^{-0.5},\; s \cdot W^{-1.5}\right)
$$

Its peak, at $s = W$, is $d^{-0.5} \cdot W^{-0.5}$. Before the peak the formula equals
$\text{peak} \times s / W$; after it, $\text{peak} \times \sqrt{W / s}$.

## Worked Example

### Part A: the learning rate at five updates

The numbers of the figure: peak = **3e-4** (0.0003), warmup $W$ = **200**, run $N$ = **2,000**,
cosine floor $f$ = **0.1**.

1. **Update 100** (warming up, since 100 < 200): $m = 100 / 200 = 0.5$.
   Learning rate = 3e-4 × 0.5 = **1.5e-4** on every schedule.
2. **Update 200** (warmup just ended): $p = (200 - 200) / 1800 = 0$.
   - linear: $1 - 0 = 1$; cosine: $0.1 + 0.9 \times \frac{1}{2}(1 + \cos 0^\circ) = 0.1 + 0.9 = 1$;
     inverse square root: $\sqrt{200 / 200} = 1$.
   - Learning rate = **3e-4**, the peak, on every schedule.
3. **Update 650:** $p = (650 - 200) / 1800 = 450 / 1800 = 0.25$.
   - linear: $m = 0.75$, so 3e-4 × 0.75 = **2.25e-4**
   - cosine: angle 45°, cos 45° ≈ 0.7071, half-wave $\frac{1}{2}(1 + 0.7071) = 0.8536$;
     $m = 0.1 + 0.9 \times 0.8536 = 0.8682$, so ≈ **2.60e-4**
   - inverse square root: $m = \sqrt{200 / 650} = \sqrt{0.3077} \approx 0.5547$, so ≈ **1.66e-4**
4. **Update 1,100:** $p = 900 / 1800 = 0.5$.
   - linear: $m = 0.5$, so **1.5e-4**
   - cosine: cos 90° = 0, half-wave 0.5; $m = 0.1 + 0.9 \times 0.5 = 0.55$, so **1.65e-4**
   - inverse square root: $m = \sqrt{200 / 1100} \approx 0.4264$, so ≈ **1.28e-4**
5. **Update 2,000** (the end): $p = 1$.
   - linear: $m = 0$, so **0**
   - cosine: cos 180° = −1, half-wave 0; $m = 0.1$, so **3e-5**
   - inverse square root: $m = \sqrt{200 / 2000} = \sqrt{0.1} \approx 0.3162$, so ≈ **9.49e-5**

### Part B: a warmup given as a share

1. `warmup_steps=0.1` in a run of 2,000 updates: $\lceil 2000 \times 0.1 \rceil = 200$ updates, the
   warmup of the figure.
2. RoBERTa's fine-tuning share, 0.06, in a run of 1,234 updates: $1234 \times 0.06 = 74.04$,
   rounded up to **75** updates.
3. `warmup_steps=1.0`: not below 1, so it is a count: **1** update.

### Part C: the original Transformer's peak

The base model: width $d$ = 512, warmup $W$ = 4,000.

1. $d \times W = 512 \times 4000 = 2{,}048{,}000$.
2. Peak $= 1 / \sqrt{2{,}048{,}000} \approx 1 / 1431 \approx$ **6.99e-4** (needs a calculator).
3. At update 8,000, twice the warmup: $\text{peak} \times \sqrt{4000 / 8000} = 6.99\text{e-}4 \times
   0.7071 \approx$ **4.94e-4**.

**What this means:** all three schedules share the warmup and the peak; after it the inverse square
root drops fastest at first, the cosine curve holds the learning rate highest for the first half of
the fall, and only the linear one reaches zero.

## What To Watch

- **The learning rate that is actually applied.** Log it at every update and compare it with the
  curve you meant: a flat start where you wanted a climb means the warmup was never set
  (`warmup_steps` defaults to 0).
- **The form of `warmup_steps`.** Below 1 it is a share of the run; from 1 up it is a count.
- **The warmup as a share of the run.** Published warmups range from 0.67% (Llama 3 405B) to 6%
  (RoBERTa-large and RoBERTa fine-tuning).
- **Where the fall ends.** `"linear"` and `"cosine"` end at zero; LLaMA and GPT-3 ended at 10% of the
  peak (`"cosine_with_min_lr"`); `"inverse_sqrt"` never reaches zero.
- **The batch size.** Goyal et al. introduced their gradual warmup for very large batches and
  compared it with no warmup at a batch of 8,192 images.
