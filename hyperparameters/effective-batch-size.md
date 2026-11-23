---
id: hyperparameter/effective-batch-size
type: hyperparameter
slug: effective-batch-size
schema_version: 3.1
title: Effective Batch Size
name: effective_batch_size
context: training
priority: 2
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
range: 8 — 8,192 examples per update
controls: 'How many training examples are averaged into each single change of the model: the batch on one device, multiplied by the number of devices and by the gradient accumulation steps.'
effects:
  decrease:
  - Fewer examples are averaged into each update
  - Updates fluctuate more from one to the next
  - More updates for each pass over the data
  - Less memory, fewer devices or fewer accumulated batches needed
  increase:
  - More examples are averaged into each update
  - Updates fluctuate less from one to the next
  - Fewer updates for each pass over the data
  - The learning rate tuned for the smaller batch may no longer fit
  - Past a limit, accuracy falls (about 8,000 images in Goyal et al.)
scale:
  min: 1
  max: 65536
  typical_min: 8
  typical_max: 8192
  spacing: log
typical_values:
- setting: Hugging Face Transformers defaults on one device (per-device batch 8, accumulation 1)
  value: '8'
  source: https://huggingface.co/docs/transformers/main_classes/trainer
- setting: 'BERT, fine-tuning on a task: batch sizes the paper lists'
  value: 16 or 32
  source: https://arxiv.org/abs/1810.04805
- setting: 'QLoRA, fine-tuning LLaMA: 16 for the 7B and 13B models, up to 64 for the 65B model'
  value: 16 to 64
  source: https://arxiv.org/abs/2305.14314
- setting: LoRA paper, all GPT-3 experiments
  value: '128'
  source: https://arxiv.org/abs/2106.09685
- setting: BERT, training from scratch (sequences of 512 tokens)
  value: '256'
  source: https://arxiv.org/abs/1810.04805
- setting: 'ResNet-50 on ImageNet: the largest batch Goyal et al. trained with no loss of accuracy'
  value: 8,192
  source: https://arxiv.org/abs/1706.02677
mistakes:
- mistake: Reading only the per-device batch size
  why: '`per_device_train_batch_size` is **one of three factors**. Hugging Face Transformers defines the effective batch as per-device batch × devices × gradient accumulation steps. A per-device batch of 4 on 2 devices with 16 accumulation steps is an effective batch of 128, not 4.'
  fix: 'Multiply the three, or read the line the `Trainer` logs at the start of a run: "Total train batch size (w. parallel, distributed & accumulation)".'
- mistake: Changing the effective batch and leaving the learning rate alone
  why: 'The two work as a pair. Goyal et al. trained ResNet-50 with 8,192 images per batch: with the learning rate left at the value tuned for 256 images (0.1), the error was **41.67%**; with the learning rate scaled up 32 times (3.2), it was **23.74%**, against 23.60% for the 256-image baseline.'
  fix: 'Apply their **linear scaling rule** as a starting point and test it: when the batch is multiplied by k, multiply the learning rate by k. They showed it for SGD on image classification, together with a warmup.'
- mistake: Assuming a larger batch is always at least as good
  why: 'Goyal et al. report that the batch cannot be scaled indefinitely: results were stable up to about 8,000 images, and beyond that accuracy degraded rapidly (error 24.79% at 16,000 images). Smith et al. note that when the batch size is increased the test accuracy often falls.'
  fix: Treat the effective batch as a setting to test, with the learning rate adjusted alongside it, and compare the results on held-out data.
- mistake: Comparing batch sizes that are counted in different units
  why: Papers on language models often count **tokens**, not sequences. BERT's batch of 256 sequences is 128,000 tokens; LLaMA reports 4M tokens. Set side by side as bare numbers, 256 and 4,000,000 say nothing.
  fix: 'Convert to one unit first: tokens per update = sequences per update × tokens per sequence.'
tags:
- training
- batch-size
- optimization
updated: '2026-11-23'
related:
- hyperparameter/gradient-accumulation
- hyperparameter/learning-rate
- hyperparameter/lr-warmup-schedule
- hyperparameter/training-duration
sources:
- url: https://huggingface.co/docs/transformers/main_classes/trainer
  title: 'Hugging Face Transformers documentation: Trainer and TrainingArguments'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py
  title: 'Hugging Face Transformers source: training_args.py (defaults and descriptions)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/trainer.py
  title: 'Hugging Face Transformers source: trainer.py (total train batch size)'
  trust: 0.9
- url: https://docs.pytorch.org/docs/2.15/notes/amp_examples.html
  title: 'PyTorch documentation: Automatic Mixed Precision examples (Gradient accumulation)'
  trust: 0.9
- url: https://arxiv.org/abs/1706.02677
  title: 'Accurate, Large Minibatch SGD: Training ImageNet in 1 Hour (Goyal et al., 2017)'
  trust: 0.9
- url: https://arxiv.org/abs/1711.00489
  title: Don't Decay the Learning Rate, Increase the Batch Size (Smith et al., 2017)
  trust: 0.9
- url: https://arxiv.org/abs/1810.04805
  title: 'BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)'
  trust: 0.9
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
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

The **effective batch size** is the number of training examples that go into **one update** of
the model.

- A **batch** is the group of examples a device looks at together.
- An **update** is one change of the model's weights (its adjustable numbers).
- A **device** is one graphics processing unit (GPU) or other processor doing the training.

It is called "effective" because it is usually **not the number you typed**. The batch on one
device is multiplied twice before an update happens:

- by the **number of devices**, when several train in parallel and their results are combined;
- by the **gradient accumulation steps**, when several batches are combined before each update.

In Hugging Face Transformers there is no setting called `effective_batch_size`. It is the product
of `per_device_train_batch_size`, the number of devices and `gradient_accumulation_steps`.

**Analogy:** asking for directions. Ask 8 passers-by and take the average of where they point:
the answer is quick but can be thrown off by one confused person. Ask 8,000 and the average is
steady, but it took far longer to collect, and you moved only once. The analogy shows the
trade-off only; the exact rules are under Calculation.

## How It Works

**Three factors make the effective batch:**

| Factor | Setting in Hugging Face Transformers | Default | What limits it |
| --- | --- | --- | --- |
| Examples on one device | `per_device_train_batch_size` | 8 | the device's memory |
| Devices in parallel | the hardware in use | 1 | how many you have |
| Batches combined per update | `gradient_accumulation_steps` | 1 | time: each one is another pass |

**What a larger effective batch changes:**

- **Each update is steadier.** An update follows the average over its examples. Smith et al.
  describe the random fluctuation of training with a **noise scale** that is, to a close
  approximation, proportional to the learning rate divided by the batch size. Doubling the batch
  halves it.
- **There are fewer updates per epoch.** An **epoch** is one full pass over the training data.
  The same data, cut into larger pieces, gives fewer pieces.
- **It can be shared across machines.** Smith et al. note that large batches can be parallelized
  across many machines, which shortens training time.

**How it is tied to the learning rate** (the size of each update's step):

- Goyal et al. state the **linear scaling rule**: when the batch size is multiplied by k,
  multiply the learning rate by k. With it, and a warmup, they matched the accuracy of a
  256-image batch at every size up to 8,192.
- The rule has a limit. Beyond about 8,000 images their accuracy degraded rapidly.
- Smith et al. reach the same pairing from the other side: lowering the learning rate and raising
  the batch size both reduce the noise scale, so a planned drop in learning rate can be replaced
  by a rise in batch size. They report the same test accuracy after the same number of epochs,
  with fewer updates.

**Batch sizes in language-model papers are often counted in tokens** (a token is a word or a
piece of a word):

| Model | Batch per update, as the paper reports it |
| --- | --- |
| Original Transformer (2017) | about 25,000 source tokens and 25,000 target tokens |
| BERT, training from scratch | 256 sequences × 512 tokens = 128,000 tokens |
| LLaMA, all four sizes | 4M tokens |
| Llama 3 405B | 4M tokens at first, doubled to 8M and later to 16M during the run |

The Llama 3 authors give the reason for the growing batch: a lower batch size early in training
improves stability, and a larger one later improves efficiency.

## Calculation

Four things are calculated: the **effective batch**, the **tokens per update**, the **updates
per epoch**, and the **noise scale**. For the learning rate that goes with a new batch size, see
the learning rate page.

### 1. The effective batch

In words: multiply the examples one device holds by the number of devices and by the number of
batches accumulated. This is the definition in the Hugging Face Transformers documentation.

$$
B_\text{eff} = b \times d \times a
$$

| Symbol | Meaning |
| --- | --- |
| $b$ | `per_device_train_batch_size`: examples in one batch on one device |
| $d$ | the number of devices training in parallel |
| $a$ | `gradient_accumulation_steps`: batches combined before each update |
| $B_\text{eff}$ | the effective batch: examples per update |

### 2. Tokens per update

In words: multiply the sequences in one update by the tokens in one sequence. The BERT paper
does this sum in its own text.

$$
T = B_\text{eff} \times L
$$

| Symbol | Meaning |
| --- | --- |
| $L$ | the length of one sequence, in tokens |
| $T$ | tokens per update |

### 3. Updates per epoch

In words: divide the number of training examples by the effective batch and round up.

$$
U = \left\lceil \frac{N}{B_\text{eff}} \right\rceil
$$

| Symbol | Meaning |
| --- | --- |
| $N$ | the number of examples in the training set |
| $U$ | updates in one epoch |
| $\lceil \; \rceil$ | "round up to the next whole number" |

### 4. The noise scale

In words: multiply the learning rate by the size of the training set and divide by the batch
size. This is the approximation Smith et al. give for a batch much smaller than the training set.

$$
g \approx \frac{\epsilon \times N}{B}
$$

| Symbol | Meaning |
| --- | --- |
| $\epsilon$ | the learning rate |
| $N$ | the number of examples in the training set |
| $B$ | the batch size |
| $g$ | the noise scale: how large the random fluctuations of training are |

The number itself has no everyday unit. It is used to **compare** two setups: equal noise scale,
similar amount of randomness.

## Worked Example

### Part A: the effective batch

1. **The Hugging Face defaults on one device:** $b = 8$, $d = 1$, $a = 1$.
   $B_\text{eff} = 8 \times 1 \times 1 = 8$.
2. **A setup limited by memory:** $b = 4$, $d = 2$, $a = 16$ (numbers picked for this example).
   $B_\text{eff} = 4 \times 2 \times 16 = 128$.

**What this means:** the second configuration shows "4" as its batch size, yet each update
averages 128 examples, sixteen times as many as the default.

### Part B: tokens per update

The numbers of the BERT paper: 256 sequences per batch, 512 tokens per sequence.

1. $T = 256 \times 512 = 128{,}000$ tokens.

**What this means:** BERT's "256" and LLaMA's "4M" are in different units. In tokens, LLaMA's
batch is 4,000,000 / 128,000 = 31.25 times BERT's.

### Part C: updates per epoch

A made-up training set of **1,000** examples.

1. Effective batch 8: 1,000 / 8 = **125 updates**.
2. Effective batch 128: 1,000 / 128 = 7.8125. Round up: **8 updates**.

**What this means:** the larger batch changes the model 8 times per pass over the data instead
of 125 times. Each of the 8 rests on more examples.

### Part D: two ways to halve the noise

The learning rate and batch size of the baseline in Goyal et al. (0.1 and 256 images), and the
size of ImageNet as Smith et al. give it (about 1,280,000 images).

1. **Baseline:** $g \approx 0.1 \times 1{,}280{,}000 / 256 = 128{,}000 / 256 = 500$.
2. **Halve the learning rate** to 0.05: $g \approx 0.05 \times 1{,}280{,}000 / 256 = 64{,}000 / 256 = 250$.
3. **Or double the batch** to 512: $g \approx 0.1 \times 1{,}280{,}000 / 512 = 128{,}000 / 512 = 250$.
4. **Double both** (batch 512, learning rate 0.2): $g \approx 0.2 \times 1{,}280{,}000 / 512 = 256{,}000 / 512 = 500$.

**What this means:** halving the learning rate and doubling the batch reduce the noise by the
same amount (steps 2 and 3). Doubling both leaves it where it was (step 4), which is the linear
scaling rule seen from the side of the noise.

## What To Watch

- **The number the trainer reports.** The Hugging Face `Trainer` logs "Total train batch size
  (w. parallel, distributed & accumulation)" when a run starts.
- **The learning rate, every time a factor changes.** Adding a device or raising the
  accumulation steps changes the effective batch as much as editing the batch size does.
- **The start of a large-batch run.** Goyal et al. found that large batches run into
  optimization difficulties early in training and added a gradual warmup: without it, their
  8,192-image run ended at 24.84% error instead of 23.74%.
- **The unit.** Examples, sequences, images and tokens are all called "batch size" in papers.
- **What each published value was used for.** The rows of the typical values table come from
  different models, optimizers and tasks. A value copied to another setup is a starting point to
  test.
- **Layers that look across the batch.** Goyal et al. note that batch normalization computes
  statistics over the batch, so changing the per-device batch changes the loss being optimized.
