---
id: hyperparameter/weight-decay
type: hyperparameter
slug: weight-decay
schema_version: 3.1
title: Weight Decay
name: weight_decay
context: training
priority: 8
importance: important
goals:
- training
- finetuning
models:
- llm
- vision
- audio
- diffusion
- rl
range: 0 — 0.3
controls: 'How strongly every weight of a model is pulled back toward zero at each training update: zero means no pull, larger values shrink the weights faster.'
effects:
  decrease:
  - Each update shrinks the weights by a smaller share
  - Weights are freer to grow large
  - At 0 the pull toward zero is switched off
  increase:
  - Each update shrinks the weights by a larger share
  - Weights stay smaller
  - A weight the training data stops pushing fades toward zero sooner
  - 'Stronger regularization: the model is held back more from fitting the training data exactly'
scale:
  min: 0
  max: 0.5
  typical_min: 0
  typical_max: 0.3
  spacing: linear
typical_values:
- setting: 'Hugging Face Transformers: default of TrainingArguments (no weight decay)'
  value: '0.0'
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py
- setting: 'PyTorch: default of the AdamW optimizer'
  value: '0.01'
  source: https://github.com/pytorch/pytorch/blob/main/torch/optim/adamw.py
- setting: BERT, training from scratch (Adam optimizer, written as L2 weight decay)
  value: '0.01'
  source: https://arxiv.org/abs/1810.04805
- setting: GPT-3, training from scratch, all model sizes
  value: '0.1'
  source: https://arxiv.org/abs/2005.14165
- setting: LLaMA, training from scratch (AdamW optimizer)
  value: '0.1'
  source: https://arxiv.org/abs/2302.13971
- setting: Vision Transformer, training from scratch on ImageNet (its smallest training set)
  value: '0.3'
  source: https://arxiv.org/abs/2010.11929
mistakes:
- mistake: Passing weight_decay to Adam and expecting the behaviour of AdamW
  why: 'In PyTorch, `torch.optim.Adam(weight_decay=...)` adds the weight to the **gradient** (an L2 penalty), while `torch.optim.AdamW` shrinks the **weight itself**. Loshchilov and Hutter prove that the two are **not equivalent** for Adam: Adam rescales every gradient, and the penalty gets rescaled with it. Part C of the worked example shows a case where the L2 form moves the weight by the same amount whether the setting is 0.1 or 0.01.'
  fix: Use `torch.optim.AdamW`, or `torch.optim.Adam(..., decoupled_weight_decay=True)`. The default optimizer of Hugging Face `TrainingArguments` is already AdamW.
- mistake: Assuming some weight decay is applied when none was set
  why: The defaults disagree. PyTorch's AdamW defaults to **0.01**, but Hugging Face `TrainingArguments` defaults to **0.0** and the Trainer passes its own value to the optimizer, so a Trainer run that never sets `weight_decay` trains with **no decay**. PyTorch's plain Adam also defaults to 0.
  fix: Set `weight_decay` explicitly and record it. The published values in the table on this page are 0.01 (BERT, RoBERTa) and 0.1 (GPT-3, LLaMA).
- mistake: Copying a weight decay value without the learning rate and run length it was used with
  why: 'In PyTorch''s AdamW each update multiplies a weight by **1 − learning rate × weight decay**, so the same setting pulls ten times more weakly at a ten times smaller learning rate. The AdamW paper also found that the best value depends on the length of the run: the more passes over the data, the **smaller** the weight decay should be.'
  fix: Take the weight decay together with the learning rate, schedule and number of updates of the source, and treat it as a starting point to test when any of those change. The AdamW paper proposes a "normalized weight decay" that divides out the run length.
- mistake: Building the optimizer by hand and decaying every parameter
  why: 'The Hugging Face Trainer does **not** decay everything: it puts biases and normalization-layer parameters (names containing `bias`, `layernorm`, `rmsnorm` or `norm`) in a second group with weight decay 0.0. `torch.optim.AdamW(model.parameters(), ...)` has no such rule and decays **all** parameters, so the same number gives a different run.'
  fix: 'Pass two parameter groups to the optimizer, as the Trainer does: one with the chosen weight decay and one, holding biases and normalization parameters, with `weight_decay: 0.0`.'
tags:
- training
- regularization
- adamw
updated: '2026-11-23'
related:
- hyperparameter/learning-rate
- hyperparameter/dropout
- hyperparameter/training-duration
- hyperparameter/adam-beta2
sources:
- url: https://arxiv.org/abs/1711.05101
  title: Decoupled Weight Decay Regularization (Loshchilov and Hutter, 2019), the AdamW paper
  trust: 0.9
- url: https://github.com/pytorch/pytorch/blob/main/torch/optim/adamw.py
  title: 'PyTorch source: torch/optim/adamw.py (AdamW defaults and algorithm)'
  trust: 0.9
- url: https://github.com/pytorch/pytorch/blob/main/torch/optim/adam.py
  title: 'PyTorch source: torch/optim/adam.py (Adam defaults, L2 penalty and decoupled decay code)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py
  title: 'Hugging Face Transformers source: training_args.py (TrainingArguments defaults)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/trainer.py
  title: 'Hugging Face Transformers source: trainer.py (parameter groups with and without weight decay)'
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
- url: https://arxiv.org/abs/2407.21783
  title: The Llama 3 Herd of Models (2024)
  trust: 0.9
- url: https://arxiv.org/abs/2010.11929
  title: 'An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale (Dosovitskiy et al., 2020), the Vision Transformer paper'
  trust: 0.9
---

## Definition

**Weight decay** is a number that sets how large a share of every weight is removed at each
training update.

- **Weights** are the numbers inside a model that training adjusts. A large language model has
  billions of them.
- An **update** is one correction: the model looks at some examples, measures how wrong it was
  (the **loss**), and moves every weight in the direction that would have made it less wrong.
  That direction, with its steepness, is the **gradient**.
- Weight decay adds a second, separate movement to every update: each weight is also
  **multiplied by a number slightly below 1**, so it shrinks a little toward zero.
- The two movements compete. A weight keeps its size only if the training data keeps pushing it
  back; a weight the data does not need drifts toward zero.

Two terms that come with it:

- **Overfitting** means a model fits its training examples so exactly that it does worse on new
  examples.
- **Regularization** is the name for any technique that holds a model back from overfitting.
  Weight decay is one. The Hugging Face documentation describes it as preventing overfitting by
  penalizing large weights, and the GPT-3 paper says it uses weight decay "to provide a small
  amount of regularization".

**Analogy:** a garden where every plant is trimmed by the same small percentage each day. A
plant that keeps growing stays large; one that has stopped growing slowly disappears. The
analogy shows the direction only; the exact rule is under Calculation.

## How It Works

**There are two ways to pull weights toward zero, and they are not the same thing.** Libraries
use the name "weight decay" for both.

| Form | What it does at each update | Where it is used |
| --- | --- | --- |
| **L2 penalty** (L2 regularization) | Adds `weight decay × weight` to the **gradient**; the optimizer then treats it like any other gradient | PyTorch `Adam` and `SGD` with `weight_decay` set |
| **Decoupled weight decay** | Shrinks the **weight** directly, outside the gradient calculation | PyTorch `AdamW`; `Adam` with `decoupled_weight_decay=True` |

Why the difference matters, as Loshchilov and Hutter (the AdamW paper) explain it:

- With plain **stochastic gradient descent (SGD)**, the optimizer that subtracts the gradient
  times the learning rate, the two forms give the same updates once the setting is rescaled by
  the learning rate.
- **Adam** is an optimizer that divides every gradient by a running measure of how large that
  weight's gradients have been. With the L2 form, the penalty is part of the gradient, so it is
  **divided too**.
- The result, in the paper's words, is that weights with large gradients "do not get regularized
  as much as they would with decoupled weight decay".
- Decoupled weight decay regularizes **all weights at the same rate**.

What the AdamW paper reports from its image-classification experiments:

- Adam with decoupled weight decay generalized better than Adam with the L2 penalty: a **15%
  relative improvement in test error**.
- Decoupling made the best learning rate and the best weight decay **much more independent** of
  each other, which makes them easier to tune one at a time.
- The best weight decay still depended on the **length of the run**: the more passes over the
  data, the smaller the value should be.

What published models used:

| Model | Weight decay | Notes from the paper |
| --- | --- | --- |
| BERT, training from scratch | 0.01 | Adam; written as "L2 weight decay" |
| RoBERTa, training from scratch | 0.01 | Same value for the base and large models |
| RoBERTa, fine-tuning | 0.1 or 0.01 | 0.1 on the RACE and GLUE tasks, 0.01 on SQuAD |
| GPT-3 | 0.1 | All model sizes |
| LLaMA | 0.1 | AdamW |
| Llama 3, scaling experiments | 0.1 | Stated as 0.1 times the learning rate at each step |
| Vision Transformer, training from scratch | 0.1, 0.03 or 0.3 | 0.1 on the largest data set (JFT-300M), 0.03 on ImageNet-21k, 0.3 on ImageNet |
| Vision Transformer, fine-tuning | none | "no weight decay" |

The Vision Transformer authors describe 0.1 as "a high weight decay" that they found useful for
transfer to other tasks, and report that strong regularization was key when training from
scratch on ImageNet, their smallest training set.

**Not every parameter is decayed.** The Hugging Face Trainer applies the setting to the weight
matrices only. Biases and the parameters of normalization layers are placed in a separate group
with weight decay 0.0.

## Calculation

Two things are calculated: **what one update does to a weight**, and **what many updates add up
to**.

### 1. One update (decoupled weight decay, as in PyTorch AdamW)

In words: first shrink the weight by the share "learning rate times weight decay"; then apply
the optimizer's usual step, which is computed from the loss gradient alone.

$$
\theta_t = \theta_{t-1} - \gamma \, \lambda \, \theta_{t-1} = (1 - \gamma \lambda)\,\theta_{t-1}
$$

| Symbol | Meaning |
| --- | --- |
| $\theta_{t-1}$ | the weight before the update |
| $\theta_t$ | the weight after the decay step (the optimizer's gradient step follows) |
| $\gamma$ | the learning rate at this update |
| $\lambda$ | the weight decay setting |
| $\gamma \lambda$ | the share of the weight that is removed |

Steps:

1. Multiply the learning rate by the weight decay: $\gamma \lambda$.
2. Subtract the result from 1. This is the **shrink factor**.
3. Multiply the weight by the shrink factor.
4. Apply the optimizer's gradient step to the shrunken weight.

This is the rule in the PyTorch documentation and code (`param.mul_(1 - lr * weight_decay)`).
The AdamW paper writes the decay step without the learning rate, multiplied only by the
schedule's multiplier; PyTorch multiplies by the learning rate itself. A value from a paper and
a value for PyTorch are therefore comparable only when the learning rates are known.

### 2. Many updates

In words: shrinking by the same factor $n$ times multiplies the weight by that factor to the
power $n$. This isolates the decay; in real training the gradient step pushes back at the same
time.

$$
\theta_n = (1 - \gamma \lambda)^{\,n} \; \theta_0
$$

| Symbol | Meaning |
| --- | --- |
| $\theta_0$ | the weight at the start |
| $n$ | the number of updates |
| $\theta_n$ | the weight after $n$ updates of decay alone, at a constant learning rate |

### 3. The L2 form, for comparison

In words: instead of shrinking the weight, add "weight decay times weight" to the gradient, and
let the optimizer process the sum. This is what PyTorch `Adam` does when `weight_decay` is set.

$$
g_t \leftarrow g_t + \lambda \, \theta_{t-1}
$$

| Symbol | Meaning |
| --- | --- |
| $g_t$ | the gradient of the loss for this weight |
| $\lambda \, \theta_{t-1}$ | the penalty added to it |

## Worked Example

### Part A: one decay step, by hand

Made-up round numbers so the arithmetic is small: a weight of **2**, a learning rate of **0.1**,
a weight decay of **0.1**.

1. Learning rate × weight decay: $0.1 \times 0.1 = 0.01$.
2. Shrink factor: $1 - 0.01 = 0.99$.
3. New weight: $2 \times 0.99 = 1.98$.
4. Amount removed: $2 - 1.98 = 0.02$, which is 1% of the weight.

### Part B: published values, over many updates

The values LLaMA reports: weight decay **0.1** and a highest learning rate of **3e-4** (0.0003)
for its 6.7 billion parameter model. The learning rate is held constant here to keep the
arithmetic simple; the real run lowers it over time.

1. Learning rate × weight decay: $0.0003 \times 0.1 = 0.00003$.
2. Shrink factor: $1 - 0.00003 = 0.99997$. One update removes 0.003% of each weight.
3. After 10,000 updates of decay alone: $0.99997^{10000} \approx 0.741$ (needs a calculator).
   A weight the gradients never touch keeps about **74%** of its size.
4. After 100,000 updates: $0.99997^{100000} \approx 0.050$. About **5%** is left.

The same calculation with the PyTorch AdamW defaults (learning rate 0.001, weight decay 0.01):

1. $0.001 \times 0.01 = 0.00001$, so the shrink factor is $0.99999$.
2. After 10,000 updates: $0.99999^{10000} \approx 0.905$. About **90%** is left.

### Part C: the L2 form and the decoupled form are different

One weight of **2**, a learning rate of **0.001**, Adam's default settings, and a loss gradient
of **0** at the first update, so that only the decay acts.

**L2 form (PyTorch `Adam`), weight decay 0.1:**

1. Penalty added to the gradient: $0.1 \times 2 = 0.2$. The gradient becomes $0 + 0.2 = 0.2$.
2. Adam divides its averaged gradient by the square root of its averaged squared gradient. At the
   first update both are built from the same single number, so the ratio is $0.2 / 0.2 = 1$.
3. Step: learning rate × ratio $= 0.001 \times 1 = 0.001$. New weight: $2 - 0.001 = 1.999$.

**L2 form, weight decay 0.01 (ten times smaller):**

1. Penalty: $0.01 \times 2 = 0.02$. Gradient: $0.02$.
2. Ratio: $0.02 / 0.02 = 1$.
3. Step: $0.001$. New weight: $1.999$. **The same as before.**

**Decoupled form (PyTorch `AdamW`):**

1. Weight decay 0.1: shrink factor $1 - 0.001 \times 0.1 = 0.9999$. New weight:
   $2 \times 0.9999 = 1.9998$.
2. Weight decay 0.01: shrink factor $1 - 0.001 \times 0.01 = 0.99999$. New weight:
   $2 \times 0.99999 = 1.99998$. **Ten times less shrinkage.**

**What this means:** in the decoupled form the setting does what its name says: ten times the
value, ten times the pull. In the L2 form Adam's rescaling can cancel the setting out, which is
the inequivalence the AdamW paper proves. The example uses one update with no loss gradient so
that the effect can be followed by hand.

## What To Watch

- **Which form the optimizer uses.** PyTorch `AdamW` decouples the decay; PyTorch `Adam` and `SGD`
  add an L2 penalty to the gradient.
- **The value that is actually applied.** Hugging Face `TrainingArguments` defaults to 0.0;
  PyTorch `AdamW` used directly defaults to 0.01.
- **The learning rate alongside it.** In PyTorch the share removed per update is the learning
  rate times the weight decay, so a learning rate schedule changes the strength of the decay
  during the run.
- **The length of the run.** The AdamW paper found that longer runs need smaller values.
- **Which parameters are decayed.** The Hugging Face Trainer leaves out biases and
  normalization-layer parameters; a hand-built optimizer does not unless it is given two groups.
- **What the value was tuned for.** Each row of the typical values belongs to one model, one
  optimizer and one learning rate. RoBERTa used 0.01 to train from scratch and 0.1 to fine-tune
  on some tasks; the Vision Transformer used no weight decay when fine-tuning.
