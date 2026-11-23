---
id: hyperparameter/gradient-clipping
type: hyperparameter
slug: gradient-clipping
schema_version: 2.2
title: "Gradient Clipping"
name: max_grad_norm
context: training
priority: 9
importance: important
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "1 — 45"
controls: "The largest overall size the combined gradient may have at one training update: a larger gradient is shrunk to this size, keeping its direction, before the model is changed."
effects:
  decrease:
    - "Clipping starts at smaller gradient sizes, so it acts on more updates"
    - "Large updates are cut down further; their direction is kept"
    - "Very small values shrink almost every update to the same size"
    - "In the Hugging Face Trainer, 0 switches clipping off altogether"
  increase:
    - "Clipping acts only on rarer, larger gradients"
    - "A sudden very large gradient passes with less reduction"
    - "Above every gradient size the run produces, clipping never acts"
scale:
  min: 0.1
  max: 100
  typical_min: 1
  typical_max: 45
  spacing: log
typical_values:
  - setting: "Hugging Face Transformers: default of TrainingArguments"
    value: "1.0"
    source: "https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py"
  - setting: "GPT-3, training from scratch, all model sizes (global norm)"
    value: "1.0"
    source: "https://arxiv.org/abs/2005.14165"
  - setting: "LLaMA, training from scratch"
    value: "1.0"
    source: "https://arxiv.org/abs/2302.13971"
  - setting: "Vision Transformer: fine-tuning, and training from scratch on ImageNet (global norm)"
    value: "1"
    source: "https://arxiv.org/abs/2010.11929"
  - setting: "Pascanu et al.: recurrent networks on their synthetic long-memory tasks"
    value: "6"
    source: "https://arxiv.org/abs/1211.5063"
  - setting: "Pascanu et al.: character-level language model, loss summed over 200-step sequences"
    value: "45"
    source: "https://arxiv.org/abs/1211.5063"
mistakes:
  - mistake: "Assuming clipping is on, or off, without checking the training loop"
    why: >-
      The two common setups differ. The Hugging Face `Trainer` clips at **1.0 by default** and skips
      clipping only when `max_grad_norm` is 0. A hand-written PyTorch loop does **no clipping at
      all** unless it calls `torch.nn.utils.clip_grad_norm_` itself.
    fix: >-
      Decide on a value and set it explicitly: `max_grad_norm` in `TrainingArguments`, or a call to
      `clip_grad_norm_(model.parameters(), max_norm)` in your own loop.
  - mistake: "Copying a threshold without the scale of the loss it was tuned for"
    why: >-
      The size of the gradient depends on how the loss is computed. Pascanu et al. used a threshold
      of **8** for music models whose loss is averaged over the sequence, and **45** for a language
      model whose loss is **summed** over 200 steps. A threshold far below the usual gradient size
      shrinks every update; one far above it never acts.
    fix: >-
      Follow their heuristic: look at the **average gradient norm over a large number of updates**
      and choose the threshold from that. The Hugging Face `Trainer` logs the norm before clipping
      as `grad_norm`.
  - mistake: "Clipping in the wrong place in the update"
    why: >-
      Clipping acts on the gradients that the backward pass has just computed, and must run before
      the optimizer uses them. Called before `loss.backward()` it sees the previous gradients or none; called
      after `optimizer.step()` the model has already moved by the unclipped amount.
    fix: >-
      Use the order the Hugging Face documentation states: backward pass, then clipping, then the
      optimizer step.
  - mistake: "Using clip_grad_value_ when clip_grad_norm_ was meant"
    why: >-
      `clip_grad_value_` caps **each number separately** at a fixed size, which can change the
      direction of the gradient. Pascanu et al. describe this element-wise form (from Mikolov) and
      chose instead to rescale the **whole gradient**, which keeps its direction. Part C of the
      worked example shows the difference.
    fix: >-
      Use `clip_grad_norm_` (what `max_grad_norm` controls) unless an element-wise cap is what you
      intend.
  - mistake: "Expecting clipping to repair a gradient that is already infinite or not a number"
    why: >-
      Clipping divides by the gradient's size. When that size is infinite or "not a number", no
      rescaling gives usable values. PyTorch does not stop by default: `error_if_nonfinite` is
      `False`.
    fix: >-
      Pass `error_if_nonfinite=True` to `clip_grad_norm_` to get an error at the step where it
      happens, and look for the cause in the data or the learning rate.
tags: [training, stability, gradient]
updated: 2026-11-23
related: [hyperparameter/learning-rate]
sources:
  - { url: "https://arxiv.org/abs/1211.5063", title: "On the difficulty of training Recurrent Neural Networks (Pascanu, Mikolov, Bengio, 2013)", trust: 0.9 }
  - { url: "https://github.com/pytorch/pytorch/blob/main/torch/nn/utils/clip_grad.py", title: "PyTorch source: torch/nn/utils/clip_grad.py (clip_grad_norm_ and clip_grad_value_)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py", title: "Hugging Face Transformers source: training_args.py (max_grad_norm default and description)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/trainer.py", title: "Hugging Face Transformers source: trainer.py (when clipping runs, grad_norm logging)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2005.14165", title: "Language Models are Few-Shot Learners (Brown et al., 2020), the GPT-3 paper", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2302.13971", title: "LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2010.11929", title: "An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale (Dosovitskiy et al., 2020), the Vision Transformer paper", trust: 0.9 }
---

## Definition

**Gradient clipping** limits how large one training update can be. `max_grad_norm` is the limit.

- **Weights** are the numbers inside a model that training adjusts.
- At every **update**, the training code computes a **gradient**: for every weight, how much and in
  which direction it should change to reduce the error (the **loss**).
- The **norm** of the gradient is one number for its overall size. Put all the gradient's numbers
  in one list, square each, add the squares, and take the square root. This is the **L2 norm**, or
  Euclidean length, the same as the length of an arrow in school geometry.
- **Clipping by norm:** if this size is above the limit, every number of the gradient is
  multiplied by the same factor so that the size becomes exactly the limit. If it is below, nothing
  changes.
- Because every number is multiplied by the same factor, the gradient keeps its **direction**; only
  its length is cut.

**Analogy:** a speed limiter on a vehicle. It does not change where you steer; it only caps how
fast you go, and below the cap it does nothing. The analogy shows the idea only; the exact rule is
under Calculation.

## How It Works

**The problem it was introduced for.** Pascanu, Mikolov and Bengio (2013) studied **recurrent
neural networks**, which process a sequence one step at a time and pass their state along. They
describe the **exploding gradients problem**: "a large increase in the norm of the gradient during
training".

Their explanation, in steps:

1. Where the gradient explodes, the error surface has a **steep wall**: the loss rises very sharply
   in one direction.
2. A normal update that reaches the wall is very large, and can throw the weights far away,
   "possibly leaving the valley and disrupting the learning process".
3. If the update is **rescaled to a fixed size** whenever its norm is above a threshold, the model
   stays close to the wall and returns to the smoother region beside it, where ordinary training
   continues.

**Their rule (Algorithm 1 of the paper):** if the norm of the gradient is at or above the
threshold, replace the gradient by threshold ÷ norm × gradient.

What the paper says about the threshold:

- It is an **additional hyperparameter**.
- "One good heuristic" is to look at the **average norm over a sufficiently large number of
  updates**.
- In their experiments training was **not very sensitive** to it, and it "behaves well even for
  rather small thresholds".
- The algorithm "can also be thought of as adapting the learning rate based on the norm of the
  gradient".

**What PyTorch does.** `torch.nn.utils.clip_grad_norm_(parameters, max_norm)`:

- computes **one norm over all the parameters together**, "as if the norms of the individual
  gradients were concatenated into a single vector" (the **global** norm);
- multiplies every gradient by $\min\bigl(\frac{\text{max\_norm}}{\text{norm} + 10^{-6}},\, 1\bigr)$;
- returns the norm measured **before** clipping.

**What the Hugging Face Trainer does.** `max_grad_norm` (default **1.0**) is passed to the
clipping function of the Accelerate library (`accelerator.clip_grad_norm_`), applied "after
backward pass, before optimizer step". A value of 0 skips clipping. The
Trainer logs the norm before clipping as `grad_norm`. The documentation lists typical values:
1.0 ("standard"), 0.5 ("more conservative"), 5.0 ("less aggressive").

**What published models used:**

| Model | Clipping | Source wording |
| --- | --- | --- |
| GPT-3 | 1.0 | "we clip the global norm of the gradient at 1.0" |
| LLaMA | 1.0 | gradient clipping of 1.0 |
| Vision Transformer | global norm 1 | for fine-tuning, and for training from scratch on ImageNet |
| Pascanu et al., synthetic tasks | 6 | recurrent networks, 50 to 100 hidden units |
| Pascanu et al., music | 8 | loss averaged over the sequence |
| Pascanu et al., language model | 45 | loss summed over the sequence |

Next to its two largest thresholds the paper notes how the loss was formed: averaged over the
sequence for the music models (8), summed over the sequence for the language model (45). A
threshold is therefore tied to the loss it was tuned with.

## Calculation

In words: measure the overall size of the gradient; if it is larger than the limit, shrink every
number of it by the same factor so that the size equals the limit.

### 1. The size of the gradient

$$
\lVert g \rVert = \sqrt{g_1^2 + g_2^2 + \dots + g_k^2}
$$

| Symbol | Meaning |
| --- | --- |
| $g_1, \dots, g_k$ | every number of the gradient, across all the model's parameters |
| $\lVert g \rVert$ | the global norm: the overall size of the gradient |

### 2. The factor and the clipped gradient (as PyTorch computes them)

$$
c = \min\!\left(\frac{M}{\lVert g \rVert + 10^{-6}},\; 1\right)
\qquad
g_i \leftarrow c \cdot g_i
$$

| Symbol | Meaning |
| --- | --- |
| $M$ | the limit: `max_grad_norm` in Hugging Face, `max_norm` in PyTorch |
| $10^{-6}$ | a tiny number PyTorch adds so that it never divides by zero |
| $c$ | the clipping factor, at most 1 |
| $g_i$ | one number of the gradient, before and after clipping |

Steps:

1. Square every number of the gradient and add the squares.
2. Take the square root: this is $\lVert g \rVert$.
3. Divide the limit by it (plus $10^{-6}$).
4. If the result is above 1, use 1 instead: the gradient is already small enough.
5. Multiply every number of the gradient by this factor.
6. The optimizer then takes its step with the clipped gradient.

## Worked Example

### Part A: a gradient above the limit

A made-up model with two parameters: one holds two numbers, the other one number. Their gradients
are **(3, 4)** and **(12)**. The limit is **1.0**, the Hugging Face default.

1. Squares: $3^2 = 9$, $4^2 = 16$, $12^2 = 144$.
2. Sum: $9 + 16 + 144 = 169$.
3. Global norm: $\sqrt{169} = 13$.
4. Factor: $1.0 / 13 \approx 0.0769$ (the $10^{-6}$ changes nothing visible here).
5. Clipped gradients: $3 \times 0.0769 \approx 0.2308$, $4 \times 0.0769 \approx 0.3077$,
   $12 \times 0.0769 \approx 0.9231$.
6. Check: $\sqrt{0.2308^2 + 0.3077^2 + 0.9231^2} \approx 1.0$, the limit.

With plain gradient descent at a learning rate of 0.1, the third weight would have moved by
$0.1 \times 12 = 1.2$; after clipping it moves by $0.1 \times 0.9231 \approx 0.092$.

### Part B: a gradient below the limit

Gradient **(0.3, 0.4)**, limit **1.0**.

1. Norm: $\sqrt{0.09 + 0.16} = \sqrt{0.25} = 0.5$.
2. Factor: $1.0 / 0.5 = 2$, which is above 1, so the factor is **1**.
3. The gradient stays **(0.3, 0.4)**.

### Part C: clipping by norm against clipping each number

The gradient of Part A, $(3, 4, 12)$, with a limit of 1.

1. **By norm** (`clip_grad_norm_`): $(0.2308, 0.3077, 0.9231)$. The numbers keep their proportions
   3 : 4 : 12.
2. **Each number capped at 1** (`clip_grad_value_`): $(1, 1, 1)$. The proportions are now
   1 : 1 : 1, so the gradient points in a **different direction**.

**What this means:** clipping by norm leaves ordinary updates untouched and shortens only the
large ones, always along the direction the gradient chose; the limit sets where "large" begins.

## What To Watch

- **The gradient norm over the run.** The Hugging Face `Trainer` logs it as `grad_norm` (measured
  before clipping); `clip_grad_norm_` returns it in your own loop. Pascanu et al. set their threshold
  from the average norm over many updates.
- **How often clipping acts.** Compare the logged norm with the limit: above it, that update was
  shrunk.
- **How the loss is computed.** A loss summed over many tokens or steps has larger gradients than
  the same loss averaged; Pascanu et al. used 45 with a sum and 8 with an average.
- **Whether clipping is on.** On by default in the Hugging Face `Trainer` (1.0, off at 0); off in
  a hand-written PyTorch loop unless called.
- **Non-finite values.** If the logged norm is infinite or not a number, clipping cannot help;
  `error_if_nonfinite=True` makes PyTorch raise an error at that step.
