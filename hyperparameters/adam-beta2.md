---
id: hyperparameter/adam-beta2
type: hyperparameter
slug: adam-beta2
schema_version: 3.1
title: Adam Beta2
name: adam_beta2
context: training
priority: 12
importance: optional
goals:
- training
- finetuning
models:
- llm
- vision
- audio
- diffusion
- rl
range: 0.95 — 0.999
controls: 'How long the Adam optimizer remembers the size of past gradients when it decides how big each weight''s step should be: closer to 1 means a longer memory.'
effects:
  decrease:
  - The running average of squared gradients covers fewer past updates
  - Step sizes follow a change in gradient size more quickly
  - The average rests on fewer gradients, so it fluctuates more
  - 'RoBERTa authors: 0.98 improved stability with large batches'
  increase:
  - The running average covers more past updates
  - Step sizes react more slowly when gradients suddenly grow or shrink
  - Larger start-up bias, which Adam's bias correction removes
  - 'Kingma and Ba: values close to 1 are needed for sparse gradients'
scale:
  min: 0.9
  max: 1
  typical_min: 0.95
  typical_max: 0.999
  spacing: linear
typical_values:
- setting: 'Adam paper (Kingma and Ba): good default for the problems they tested'
  value: '0.999'
  source: https://arxiv.org/abs/1412.6980
- setting: 'PyTorch Adam and AdamW, and Hugging Face TrainingArguments: default'
  value: '0.999'
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py
- setting: Original Transformer (2017)
  value: '0.98'
  source: https://arxiv.org/abs/1706.03762
- setting: RoBERTa, training from scratch with batches of 8,000 sequences
  value: '0.98'
  source: https://arxiv.org/abs/1907.11692
- setting: GPT-3, all model sizes
  value: '0.95'
  source: https://arxiv.org/abs/2005.14165
- setting: LLaMA (AdamW optimizer)
  value: '0.95'
  source: https://arxiv.org/abs/2302.13971
mistakes:
- mistake: Assuming the library default matches the recipe being reproduced
  why: 'PyTorch''s `Adam` and `AdamW` and Hugging Face `TrainingArguments` all default to **0.999**, the Adam paper''s suggestion. Several published language models used other values: **0.98** for the original Transformer and RoBERTa, **0.95** for GPT-3 and LLaMA. A run that leaves the default in place is not the published recipe.'
  fix: Set `adam_beta2` (Hugging Face) or `betas=(beta1, beta2)` (PyTorch) explicitly to the value of the source you follow, and record it.
- mistake: Changing beta2 and leaving epsilon at its default
  why: 'The two sit in the same part of the formula: the step is divided by the square root of the squared-gradient average **plus epsilon**. The RoBERTa authors found training "very sensitive" to epsilon and paired **β2 = 0.98 with ε = 1e-6**. The original Transformer used **ε = 1e-9** and GPT-3 **ε = 1e-8**, the library default.'
  fix: Copy beta2 and epsilon **as a pair** from the source; in Hugging Face, `adam_epsilon` sits next to `adam_beta2`.
- mistake: Treating 0.99, 0.995 and 0.999 as small differences
  why: 'What matters is the **distance from 1**, because it sets how fast old gradients are forgotten. 1 − 0.99 = 0.01 and 1 − 0.999 = 0.001: the second forgets ten times more slowly. Part B of the worked example puts numbers on this: half of the average''s weight lies in the last 693 updates at 0.999 and in the last 14 at 0.95.'
  fix: Compare candidate values by **1 − β2** (or by the half-life in Part B), which works on a scale of factors of ten.
- mistake: Writing an Adam update without the bias correction
  why: The running averages start at zero, so early on they are too small. Kingma and Ba show that without the correction step, values of β2 close to 1 "lead to instabilities in training", especially in the first epochs, and that dropping it makes the method a version of RMSProp with momentum. Part A of the worked example shows a first step about 3.16 times too large.
  fix: Use the library optimizers, which divide by **1 − β2 to the power t** (PyTorch's `Adam` and `AdamW` do), or include that division in your own code.
tags:
- training
- optimizer
- adam
updated: '2026-11-23'
related:
- hyperparameter/learning-rate
- hyperparameter/effective-batch-size
sources:
- url: https://arxiv.org/abs/1412.6980
  title: 'Adam: A Method for Stochastic Optimization (Kingma and Ba, 2015)'
  trust: 0.9
- url: https://github.com/pytorch/pytorch/blob/main/torch/optim/adam.py
  title: 'PyTorch source: torch/optim/adam.py (Adam defaults and algorithm)'
  trust: 0.9
- url: https://github.com/pytorch/pytorch/blob/main/torch/optim/adamw.py
  title: 'PyTorch source: torch/optim/adamw.py (AdamW defaults)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py
  title: 'Hugging Face Transformers source: training_args.py (adam_beta1, adam_beta2, adam_epsilon defaults)'
  trust: 0.9
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.9
- url: https://arxiv.org/abs/1810.04805
  title: 'BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)'
  trust: 0.9
- url: https://arxiv.org/abs/1907.11692
  title: 'RoBERTa: A Robustly Optimized BERT Pretraining Approach (Liu et al., 2019)'
  trust: 0.9
- url: https://arxiv.org/abs/2005.14165
  title: Language Models are Few-Shot Learners (Brown et al., 2020), the GPT-3 paper
  trust: 0.9
- url: https://arxiv.org/abs/2302.13971
  title: 'LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)'
  trust: 0.9
- url: https://arxiv.org/abs/2010.11929
  title: 'An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale (Dosovitskiy et al., 2020), the Vision Transformer paper'
  trust: 0.9
---

## Definition

**Adam beta2** (written β2) sets how long the Adam optimizer remembers the **size** of past
gradients.

- **Weights** are the numbers inside a model that training adjusts.
- At every **update**, training computes a **gradient**: for every weight, which way and how
  strongly it should change to reduce the error.
- An **optimizer** is the rule that turns gradients into changes of the weights. **Adam**
  (Kingma and Ba, 2015; the name comes from "adaptive moment estimation") is one such rule;
  **AdamW** is Adam with a different form of weight decay and the same β2. AdamW is the default
  optimizer of the Hugging Face `Trainer`.
- Adam keeps, for every weight, a **running average of its squared gradients**. Squaring makes
  every value positive, so this average measures how **large** that weight's gradients have been,
  whatever their sign. The paper calls it the second raw moment, or uncentered variance.
- Adam **divides** each step by the square root of that average. A weight whose gradients have
  been large gets smaller steps; one whose gradients have been small gets larger steps.
- β2 is the **decay rate** of that average: the share of the old average kept at each update. At
  0.999, 99.9% of the old average is kept and the new squared gradient adds 0.1%.

A second setting, **β1** (default 0.9), does the same for the running average of the gradients
themselves (their direction), which is a page of its own.

**Analogy:** a long-term and a short-term memory of how bumpy a road has been. With a long memory
(β2 near 1), one sudden pothole hardly changes your idea of the road; with a short memory, you slow
down at once. The analogy shows the trade-off only; the exact rule is under Calculation.

## How It Works

**How an average with a decay rate works.** At each update the average is updated as:
new average = β2 × old average + (1 − β2) × newest squared gradient.

- The newest gradient enters with weight $1 - \beta_2$.
- At every later update its weight is multiplied by β2 again, so it **fades geometrically**.
- The Adam paper writes the result as a sum over all past gradients, each weighted by
  $(1 - \beta_2)\,\beta_2^{\,\text{age}}$.

**The start-up bias, and its correction.** Adam starts the average at **zero**. In the first
updates the average is therefore far below the true size of the gradients, and more so the closer
β2 is to 1. Kingma and Ba show that the expected average after t updates is the true value times
$1 - \beta_2^{\,t}$, and divide by that factor to remove the bias. Without this correction, they
report, values of β2 close to 1 lead to instabilities in training.

**How large a step can get.** Kingma and Ba show that the step is roughly bounded by the learning
rate, with one exception: in the most extreme case of a gradient that was zero at every earlier
update, the bound is learning rate × (1 − β1) / √(1 − β2). With β1 = 0.9:

| β2 | (1 − β1) / √(1 − β2) | Bound in that extreme case |
| --- | --- | --- |
| 0.999 | 0.1 / 0.0316 ≈ 3.16 | about 3.16 × the learning rate |
| 0.98 | 0.1 / 0.141 ≈ 0.71 | the learning rate (the other bound applies) |
| 0.95 | 0.1 / 0.224 ≈ 0.45 | the learning rate (the other bound applies) |

The paper uses the first bound only when (1 − β1) is larger than √(1 − β2); otherwise the step
is at most the learning rate.

**What the sources recommend:**

- **Kingma and Ba:** 0.999 as a good default "for the tested machine learning problems". They
  add that values close to 1 are required for **sparse gradients** (gradients that are zero most of
  the time), which makes the bias correction important.
- **RoBERTa (Liu et al.):** "setting β2 = 0.98" improved stability "when training with large
  batch sizes". The original BERT, which they reproduced, used 0.999.

**What published models used:**

| Model | β1 | β2 | ε (epsilon) |
| --- | --- | --- | --- |
| BERT | 0.9 | 0.999 | 1e-6 (as RoBERTa reports BERT's settings) |
| Vision Transformer | 0.9 | 0.999 | not stated |
| Original Transformer | 0.9 | 0.98 | 1e-9 |
| RoBERTa | 0.9 | 0.98 | 1e-6 |
| GPT-3 | 0.9 | 0.95 | 1e-8 |
| LLaMA | 0.9 | 0.95 | not stated |

## Calculation

Three things are calculated: the **running average**, its **bias correction**, and **how much
of the average comes from recent updates**.

### 1. One Adam update

In words: update the average of squared gradients, correct it for its zero start, and divide the
step by its square root.

$$
v_t = \beta_2\, v_{t-1} + (1 - \beta_2)\, g_t^2
\qquad
\hat{v}_t = \frac{v_t}{1 - \beta_2^{\,t}}
\qquad
\theta_t = \theta_{t-1} - \alpha \,\frac{\hat{m}_t}{\sqrt{\hat{v}_t} + \epsilon}
$$

| Symbol | Meaning |
| --- | --- |
| $t$ | the number of the update, counted from 1 |
| $g_t$ | the gradient of one weight at update $t$ |
| $v_t$ | the running average of squared gradients (starts at $v_0 = 0$) |
| $\beta_2$ | the setting on this page |
| $\hat{v}_t$ | the bias-corrected average |
| $\hat{m}_t$ | the bias-corrected running average of the gradients, built the same way with $\beta_1$ |
| $\alpha$ | the learning rate |
| $\epsilon$ | a small number that prevents division by zero (default 1e-8) |
| $\theta_t$ | the weight after the update |

Steps:

1. Square the new gradient: $g_t^2$.
2. Mix it into the average: keep $\beta_2$ of the old average and add $1 - \beta_2$ of the new
   square.
3. Divide by $1 - \beta_2^{\,t}$ to correct the zero start.
4. Take the square root and add $\epsilon$.
5. Divide the corrected gradient average $\hat{m}_t$ by it, multiply by the learning rate, and
   subtract from the weight.

### 2. How much of the average is recent

In words: the weights of the last n updates add up to $1 - \beta_2^{\,n}$ of the whole average.
The **half-life** is the number of updates that carry half of it.

$$
\text{share of the last } n = 1 - \beta_2^{\,n}
\qquad
\text{half-life} = \frac{\ln 0.5}{\ln \beta_2}
$$

| Symbol | Meaning |
| --- | --- |
| $n$ | a number of most recent updates |
| $\ln$ | the natural logarithm (a calculator key) |

## Worked Example

### Part A: the first update, with and without bias correction

One weight, first update ($t = 1$), gradient $g_1 = 2$, default settings β1 = 0.9, β2 = 0.999.

1. Squared gradient: $2^2 = 4$.
2. Average: $v_1 = 0.999 \times 0 + 0.001 \times 4 = 0.004$. Far below the true 4.
3. Correction: $\hat{v}_1 = 0.004 / (1 - 0.999^1) = 0.004 / 0.001 = 4$. Square root: 2.
4. Gradient average, the same way: $m_1 = 0.1 \times 2 = 0.2$; corrected
   $\hat{m}_1 = 0.2 / (1 - 0.9) = 2$.
5. **With correction:** step = learning rate × $2 / 2$ = **1 × the learning rate**.
6. **Without correction:** step = learning rate × $0.2 / \sqrt{0.004} = 0.2 / 0.0632$ ≈
   **3.16 × the learning rate**.

### Part B: how long the memory is

The three values of the table above (needs a calculator for the powers):

| β2 | Share of the average from the last 10 updates: $1 - \beta_2^{10}$ | Half-life: $\ln 0.5 / \ln \beta_2$ |
| --- | --- | --- |
| 0.999 | $1 - 0.990 = 0.010$, about **1%** | about **693** updates |
| 0.98 | $1 - 0.817 = 0.183$, about **18%** | about **34** updates |
| 0.95 | $1 - 0.599 = 0.401$, about **40%** | about **14** updates |

### Part C: a sudden jump in gradient size

A weight whose gradient has been **1** at every update for a long time, so both averages have
settled at 1 (and the bias correction no longer matters). Then the gradient jumps to **3** and stays
there. What step does Adam take at the 10th update after the jump? β1 = 0.9.

1. Gradient average: each update moves it toward 3, keeping a share 0.9 of the gap.
   $m = 3 - (3 - 1) \times 0.9^{10} = 3 - 2 \times 0.349 = 2.303$.
2. Squared-gradient average, moving from 1 toward $3^2 = 9$:
   $v = 9 - (9 - 1) \times \beta_2^{\,10}$.
   - β2 = 0.999: $v = 9 - 8 \times 0.990 = 1.080$; square root $1.039$.
   - β2 = 0.95: $v = 9 - 8 \times 0.599 = 4.210$; square root $2.052$.
3. Step, as a multiple of the learning rate:
   - β2 = 0.999: $2.303 / 1.039$ ≈ **2.22**
   - β2 = 0.95: $2.303 / 2.052$ ≈ **1.12**

**What this means:** with β2 = 0.999 the memory of small gradients lasts hundreds of updates, so
after a jump the steps run at over twice their usual size; at 0.95 the average catches up within
tens of updates and the steps stay near the learning rate.

## What To Watch

- **The value actually in use.** PyTorch and Hugging Face default to 0.999; set it explicitly when
  following a recipe that used 0.98 or 0.95.
- **Epsilon alongside it.** RoBERTa found training very sensitive to epsilon and changed both;
  published values range from 1e-9 (original Transformer) to 1e-6 (RoBERTa).
- **The batch size.** The RoBERTa authors tie their choice of 0.98 to large batches.
- **Comparisons on the right scale.** Compare values by 1 − β2: 0.999 forgets ten times more slowly
  than 0.99.
- **Your own optimizer code.** Keep the bias correction; Kingma and Ba show instabilities without it
  when β2 is close to 1.
