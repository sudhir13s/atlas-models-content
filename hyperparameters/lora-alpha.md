---
id: hyperparameter/lora-alpha
type: hyperparameter
slug: lora-alpha
schema_version: 2.2
title: "LoRA Alpha"
name: lora_alpha
context: training
priority: 6
importance: important
goals: [finetuning]
models: [llm, vision, audio, diffusion]
range: "8 — 32"
controls: "A number that sets how strongly a LoRA adapter's output is added to the frozen model: the adapter's output is multiplied by alpha divided by the rank r."
effects:
  decrease:
    - "The adapter's output is multiplied by a smaller factor"
    - "Each change to the adapter moves the model's output less"
    - "At alpha equal to r, the factor is 1"
  increase:
    - "The adapter's output is multiplied by a larger factor"
    - "Each change to the adapter moves the model's output more"
    - "With Adam, the LoRA paper finds this roughly the same as raising the learning rate"
scale:
  min: 1
  max: 256
  typical_min: 8
  typical_max: 32
  spacing: log
typical_values:
  - setting: "Hugging Face PEFT: default of LoraConfig (with its default r = 8, a factor of 1)"
    value: "8"
    source: "https://github.com/huggingface/peft/blob/main/src/peft/tuners/lora/config.py"
  - setting: "LoRA paper: RoBERTa base and DeBERTa XXL on GLUE, with r = 8 (a factor of 1)"
    value: "8"
    source: "https://arxiv.org/abs/2106.09685"
  - setting: "LoRA paper: RoBERTa large on GLUE, with r = 8 (a factor of 2)"
    value: "16"
    source: "https://arxiv.org/abs/2106.09685"
  - setting: "LoRA paper: GPT-2 on text generation tasks, with r = 4 (a factor of 8)"
    value: "32"
    source: "https://arxiv.org/abs/2106.09685"
  - setting: "Alpaca-LoRA training script: default, with r = 8 (a factor of 2)"
    value: "16"
    source: "https://github.com/tloen/alpaca-lora/blob/main/finetune.py"
  - setting: "QLoRA: LLaMA fine-tuning, with r = 64 (a factor of 0.25)"
    value: "16"
    source: "https://arxiv.org/abs/2305.14314"
mistakes:
  - mistake: "Changing r and leaving lora_alpha where it was"
    why: >-
      The factor is **alpha / r**, so it moves whenever r moves. With alpha = 16, going from
      r = 8 to r = 64 shrinks the factor from 2 to 0.25, eight times smaller (worked example,
      Part C). Kalajdzievski reports that this factor **slows learning** at higher ranks.
    fix: >-
      Decide what factor you want and set alpha to factor × r, or set `use_rslora=True` in PEFT,
      which divides by √r and changes the factor far less as r grows.
  - mistake: "Tuning lora_alpha and the learning rate as two separate dials"
    why: >-
      The LoRA paper states that, when optimizing with Adam, **tuning alpha is roughly the same as
      tuning the learning rate** (with the initialization scaled to match). Searching both at once
      repeats much of the same search.
    fix: >-
      Fix one and tune the other. The LoRA authors set alpha to the **first r they tried** and did
      not tune it, and report that this reduces the need to retune when r changes.
  - mistake: "Comparing alpha values from runs with different ranks"
    why: >-
      Alpha alone does not say how strong the adapter is. QLoRA and Alpaca-LoRA both use
      **alpha = 16**, but with r = 64 and r = 8 the factors are **0.25** and **2**.
    fix: "Compare alpha / r (or alpha / √r with rsLoRA), not alpha."
  - mistake: "Turning on use_rslora and keeping an alpha chosen for the alpha / r factor"
    why: >-
      PEFT's code sets the factor to `lora_alpha / math.sqrt(r)` with rsLoRA and to
      `lora_alpha / r` without it. At r = 64 and alpha = 16, the factor jumps from 0.25 to 2,
      **eight times** larger.
    fix: "Recompute the factor after switching, and set alpha for the factor you want."
tags: [finetuning, lora, peft, scaling]
updated: 2026-11-23
related: [hyperparameter/lora-r, hyperparameter/learning-rate, hyperparameter/target-modules]
sources:
  - { url: "https://arxiv.org/abs/2106.09685", title: "LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2312.03732", title: "A Rank Stabilization Scaling Factor for Fine-Tuning with LoRA (Kalajdzievski, 2023)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2305.14314", title: "QLoRA: Efficient Finetuning of Quantized LLMs (Dettmers et al., 2023)", trust: 0.9 }
  - { url: "https://github.com/huggingface/peft/blob/main/src/peft/tuners/lora/config.py", title: "Hugging Face PEFT source: LoraConfig (defaults, use_rslora)", trust: 0.9 }
  - { url: "https://github.com/huggingface/peft/blob/main/src/peft/tuners/lora/layer.py", title: "Hugging Face PEFT source: LoRA layer (scaling)", trust: 0.9 }
  - { url: "https://huggingface.co/docs/peft/developer_guides/lora", title: "Hugging Face PEFT documentation: LoRA", trust: 0.9 }
  - { url: "https://github.com/tloen/alpaca-lora/blob/main/finetune.py", title: "Alpaca-LoRA training script (finetune.py)", trust: 0.8 }
---

## Definition

**LoRA alpha** (written $\alpha$, the Greek letter alpha) is a fixed number that sets how strongly
a Low-Rank Adaptation (LoRA) adapter changes the model.

- **LoRA** fine-tunes (further trains) a model by keeping its large weight matrices **frozen** and
  training a small **adapter** next to each: two thin matrices whose product is a change to add.
- The **rank** r is the inner size of those two thin matrices; the LoRA rank page covers it.
- The adapter's output is **multiplied by $\alpha / r$** before it is added to the frozen layer's
  output. That multiplier is called the **scaling factor**.
- Alpha is a constant: it does not change during training and is not learned.

In Hugging Face PEFT the setting is `lora_alpha` in `LoraConfig`, and its default is **8**, the
same as the default rank, so the default factor is $8 / 8 = 1$.

**Analogy:** the volume knob on a second microphone. The frozen model is the main microphone; the
adapter is the second one; alpha / r is how far its knob is turned up before the two are mixed.
The analogy shows the role only; the exact rule is under Calculation.

## How It Works

The scale above shows alpha on a logarithmic axis (each tick a doubling), with the published
values from the table marked. **Alpha means little on its own**: each row's factor depends on its
rank, so the setting column gives both.

### Where the factor is applied

The LoRA paper (Hu et al.) adds a trained change $BA$ to a frozen matrix $W_0$ and computes a
layer's output as $h = W_0 x + BAx$. It then writes:

1. The change $BAx$ is **scaled by $\alpha / r$**, where alpha is a constant in r.
2. When optimizing with **Adam**, tuning alpha is roughly the same as tuning the **learning
   rate**, if the initialization is scaled to match.
3. So the authors **set alpha to the first r they try and do not tune it**.
4. They report that this scaling **reduces the need to retune** other settings when r changes.

PEFT's LoRA layer does exactly this: `result = result + lora_B(lora_A(dropout(x))) * scaling`,
with `scaling = lora_alpha / r`.

### Why the factor matters during training

- At the start, the adapter's $B$ matrix is all zeros, so the adapter adds nothing.
- As training changes $A$ and $B$, the factor decides how much each change shows up in the
  model's output. A factor twice as large makes the same adapter change the output twice as much.
- This is why the LoRA paper compares alpha with the learning rate, which also sets how far each
  update moves the model.

### The rank-stabilized alternative (rsLoRA)

Kalajdzievski studied the factor $\alpha / r$:

- The paper reports that dividing by r is **overly aggressive**: as the rank grows, the
  adapter's gradients **collapse**, learning **slows**, and higher ranks show little to no gain.
- It proves that adapters stay stable across ranks only if the factor shrinks like
  $1 / \sqrt{r}$, and proposes $\alpha / \sqrt{r}$.
- PEFT offers it as `use_rslora=True` (default `False`). Its documentation says this
  **stabilizes the adapters** and increases the performance potential from using a higher r.

| PEFT setting | Factor | Source |
| --- | --- | --- |
| `use_rslora=False` (default) | $\alpha / r$ | LoRA paper; PEFT `layer.py` |
| `use_rslora=True` | $\alpha / \sqrt{r}$ | rsLoRA paper; PEFT `layer.py` |

## Calculation

Two things are calculated: **the scaling factor**, and **what the layer outputs** with it.

### 1. The scaling factor

In words: divide alpha by the rank, or, with rsLoRA, by the square root of the rank.

$$
s = \frac{\alpha}{r} \qquad \text{or, with rsLoRA,} \qquad s = \frac{\alpha}{\sqrt{r}}
$$

| Symbol | Meaning |
| --- | --- |
| $\alpha$ | `lora_alpha` |
| $r$ | the LoRA rank, `r` |
| $\sqrt{r}$ | the square root of r: the number that, multiplied by itself, gives r |
| $s$ | the scaling factor applied to the adapter's output |

### 2. The layer's output

In words: compute the frozen layer's output, compute the adapter's output, multiply the second by
the factor, and add the two.

$$
h = W_0 x + s \cdot BAx
$$

| Symbol | Meaning |
| --- | --- |
| $x$ | the input to the layer |
| $W_0 x$ | the frozen layer's output |
| $BAx$ | the adapter's output, before scaling |
| $h$ | the layer's output |

Steps:

1. Compute $s$ from alpha and r.
2. Compute the adapter's output $BAx$.
3. Multiply it by $s$.
4. Add the result to the frozen output $W_0 x$.

### 3. Alpha for a chosen factor

In words: to get a factor $s$ at rank r, multiply them.

$$
\alpha = s \times r \qquad \text{or, with rsLoRA,} \qquad \alpha = s \times \sqrt{r}
$$

## Worked Example

### Part A: one output number

Made-up numbers small enough to do by hand: one output of the frozen layer is $W_0 x = 10$, and
the adapter's output for the same position is $BAx = 0.5$. Rank $r = 8$.

1. **alpha = 8:** $s = 8 / 8 = 1$. Output $h = 10 + 1 \times 0.5 = 10.5$.
2. **alpha = 16:** $s = 16 / 8 = 2$. Output $h = 10 + 2 \times 0.5 = 11$.
3. **alpha = 32:** $s = 32 / 8 = 4$. Output $h = 10 + 4 \times 0.5 = 12$.

**What this means:** doubling alpha doubles what the adapter adds (0.5, then 1, then 2), with the
adapter itself unchanged.

### Part B: the factors behind the published values

| Who | alpha | r | Factor $\alpha / r$ |
| --- | --- | --- | --- |
| PEFT default | 8 | 8 | 8 / 8 = 1 |
| LoRA paper, RoBERTa base | 8 | 8 | 8 / 8 = 1 |
| LoRA paper, RoBERTa large | 16 | 8 | 16 / 8 = 2 |
| LoRA paper, GPT-2 | 32 | 4 | 32 / 4 = 8 |
| Alpaca-LoRA | 16 | 8 | 16 / 8 = 2 |
| QLoRA, LLaMA | 16 | 64 | 16 / 64 = 0.25 |

**What this means:** the same alpha of 16 gives a factor of 2 in one script and 0.25 in another.
The published factors span 0.25 to 8, a 32-fold range.

### Part C: raising the rank with alpha kept at 16

1. **Plain LoRA, r = 8:** $s = 16 / 8 = 2$.
2. **Plain LoRA, r = 64:** $s = 16 / 64 = 0.25$. Change: $2 / 0.25 = 8$ times smaller.
3. **rsLoRA, r = 8:** $\sqrt{8} \approx 2.828$, so $s = 16 / 2.828 \approx 5.66$.
4. **rsLoRA, r = 64:** $\sqrt{64} = 8$, so $s = 16 / 8 = 2$. Change: $5.66 / 2 \approx 2.83$
   times smaller.

**What this means:** an eightfold rise in rank cuts the plain factor eightfold but the rsLoRA
factor only by $\sqrt{8} \approx 2.83$. QLoRA's own setting (r = 64, alpha = 16) gives 0.25 in
plain LoRA and 2 with rsLoRA.

### Part D: keeping a factor of 2 at r = 64

1. **Plain LoRA:** $\alpha = 2 \times 64 = 128$. Check: $128 / 64 = 2$.
2. **rsLoRA:** $\alpha = 2 \times \sqrt{64} = 2 \times 8 = 16$. Check: $16 / 8 = 2$.

**What this means:** to change r without changing the adapter's strength, change alpha with it.

## What To Watch

- **The factor, not alpha.** Write down alpha / r (or alpha / √r) for every run you compare.
- **use_rslora.** It is `False` by default in PEFT; switching it changes the factor by √r for the
  same alpha (PEFT `layer.py`).
- **The learning rate.** With Adam the LoRA paper finds alpha and the learning rate do much the
  same job; change one at a time.
- **The rank.** Any change of r changes the factor; see the LoRA rank page.
