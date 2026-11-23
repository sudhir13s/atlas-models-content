---
id: hyperparameter/dropout
type: hyperparameter
slug: dropout
schema_version: 2.2
title: "Dropout"
name: dropout
context: training
priority: 13
importance: optional
goals: [training, finetuning]
models: [llm, vision, audio, rl]
range: "0 — 0.5"
controls: "The share of a layer's outputs that are switched off at random at every training step, so the network cannot depend on any one of them; at evaluation nothing is switched off."
effects:
  decrease:
    - "Fewer outputs are switched off at each training step"
    - "Weaker regularization: more room to fit the training data too exactly"
    - "Less noise in each update, so training needs fewer updates"
    - "At 0 dropout is off and the layer passes everything through"
  increase:
    - "More outputs are switched off at each training step"
    - "Stronger regularization against overfitting"
    - "Noisier updates: Srivastava et al. report 2 to 3 times longer training"
    - "Too high, and the network underfits: the training error rises too"
scale:
  min: 0
  max: 1
  typical_min: 0
  typical_max: 0.5
  spacing: linear
typical_values:
  - setting: "PyTorch: default of torch.nn.Dropout (also the share of hidden units Srivastava et al. drop)"
    value: "0.5"
    source: "https://github.com/pytorch/pytorch/blob/main/torch/nn/modules/dropout.py"
  - setting: "Srivastava et al.: input units with real-valued inputs such as image patches (they keep 80%)"
    value: "0.2"
    source: "https://jmlr.org/papers/v15/srivastava14a.html"
  - setting: "Original Transformer, big model, English-to-German translation"
    value: "0.3"
    source: "https://arxiv.org/abs/1706.03762"
  - setting: "BERT, all layers, in training from scratch and in fine-tuning"
    value: "0.1"
    source: "https://arxiv.org/abs/1810.04805"
  - setting: "Vision Transformer, training from scratch on JFT-300M (its largest data set)"
    value: "0.0"
    source: "https://arxiv.org/abs/2010.11929"
  - setting: "Hugging Face Transformers: default attention_dropout of LlamaConfig"
    value: "0.0"
    source: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/configuration_llama.py"
mistakes:
  - mistake: "Reading the p of the dropout paper as the p of PyTorch"
    why: >-
      The two use the same letter for **opposite things**. In Srivastava et al., p is the
      probability of **keeping** a unit ("p = 1, implies no dropout"). In PyTorch, `p` of
      `torch.nn.Dropout` is the probability of an element being **zeroed**. The paper's input-layer
      value of 0.8 copied into PyTorch as `p=0.8` switches off 80% of the inputs instead of 20%.
    fix: >-
      Convert: PyTorch `p` = **1 − the paper's p**. The paper's 0.5 for hidden units stays 0.5; its
      0.8 for inputs becomes `p=0.2`. When a source does not say which convention it uses, check
      whether its "p" is described as kept or dropped.
  - mistake: "Evaluating or serving a model that is still in training mode"
    why: >-
      PyTorch's dropout layer zeroes elements only while the module is in training mode; in
      evaluation mode it "simply computes an identity function". A model left in training mode
      keeps switching outputs off at random, so the same input gives a **different answer on every
      call**.
    fix: >-
      Call `model.eval()` before evaluating or serving (it is the same as `model.train(False)`)
      and `model.train()` before training resumes.
  - mistake: "Raising the dropout rate without making the layer wider"
    why: >-
      With dropout, only a share of a layer's units is present at each step. Srivastava et al. found
      that a small keep probability on a layer of fixed size led to **underfitting**: the training
      error was high too (their Figure 9a). When they enlarged the layers to keep the expected
      number of present units fixed, the error at a keep probability of 0.1 fell from 2.7% to 1.7%.
    fix: >-
      Follow their heuristic: if n units are right for a layer without dropout, use **at least
      n / (keep probability)** units with dropout. In PyTorch terms that is n / (1 − p).
  - mistake: "Copying a dropout rate from a recipe trained on a much smaller or much larger data set"
    why: >-
      The benefit depends on the amount of data. Srivastava et al. found no improvement on their
      two smallest MNIST subsets (100 and 500 examples), a gain that grew with more data "up to a
      point and then declines". The Vision Transformer authors used dropout **0.1** on ImageNet and
      ImageNet-21k but **0.0** on JFT-300M, their largest data set.
    fix: >-
      Take the rate together with the data set size it was tuned for, and compare training and
      validation error before keeping it: dropout is there for the gap between the two.
  - mistake: "Expecting a dropout run to finish in the same number of updates"
    why: >-
      Srivastava et al. report that a dropout network typically takes **2 to 3 times longer** to
      train than a standard network of the same architecture, because each update trains a
      different random sub-network and the updates are very noisy.
    fix: >-
      Budget more updates when adding dropout, and compare runs at their best validation error, not
      at the same step count.
tags: [training, regularization, dropout]
updated: 2026-11-23
related: [hyperparameter/weight-decay, hyperparameter/training-duration, hyperparameter/learning-rate]
sources:
  - { url: "https://jmlr.org/papers/v15/srivastava14a.html", title: "Dropout: A Simple Way to Prevent Neural Networks from Overfitting (Srivastava, Hinton, Krizhevsky, Sutskever, Salakhutdinov; JMLR 2014)", trust: 0.9 }
  - { url: "https://github.com/pytorch/pytorch/blob/main/torch/nn/modules/dropout.py", title: "PyTorch source: torch/nn/modules/dropout.py (torch.nn.Dropout default and scaling)", trust: 0.9 }
  - { url: "https://github.com/pytorch/pytorch/blob/main/torch/nn/modules/module.py", title: "PyTorch source: torch/nn/modules/module.py (Module.eval and Module.train)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1810.04805", title: "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/bert/configuration_bert.py", title: "Hugging Face Transformers source: configuration_bert.py (BertConfig dropout defaults)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2010.11929", title: "An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale (Dosovitskiy et al., 2020), the Vision Transformer paper", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/configuration_llama.py", title: "Hugging Face Transformers source: configuration_llama.py (LlamaConfig attention_dropout default)", trust: 0.9 }
---

## Definition

**Dropout** switches off a random share of a layer's outputs at every training step. The
setting is that share.

- A **neural network** is built from layers of **units**: small calculations that each pass a
  number (their **output**, or **activation**) to the next layer.
- With dropout, at every training step each output is **set to zero** with a fixed probability,
  independently of the others. The survivors are passed on as usual.
- A new random choice is made at **every step**, so each step trains a slightly different,
  "thinned" version of the network.
- At **evaluation** (and when the model is used) nothing is switched off.

Two terms that come with it:

- **Overfitting** means a model fits its training examples so exactly that it does worse on new
  examples.
- **Regularization** is the name for any technique that holds a model back from overfitting.
  Dropout is one.

Why it helps, in the words of the paper that introduced it (Srivastava et al., 2014): it
"prevents units from co-adapting too much". **Co-adapting** means a unit learns to work only
together with particular other units, fixing their mistakes. If any partner may be missing at any
step, each unit has to be useful on its own.

**Analogy (from the paper):** the authors compare it to the theory that sexual reproduction
breaks up sets of genes that only work together, so each gene must be useful with a random set of
partners. They add a second picture: ten small conspiracies of five people are more likely to
survive changing conditions than one big conspiracy that needs fifty people to all play their part.

## How It Works

**One step of dropout:**

1. The layer computes its outputs as usual.
2. For each output, a random draw decides: **keep** it, or **set it to zero**.
3. The kept outputs are **scaled up** so that their average size does not change (see below).
4. The next layer receives this thinned set of outputs.
5. During learning, the gradients (the corrections) flow back only through the kept outputs.

**Why the scaling is needed.** Switching off part of the outputs makes their total smaller during
training than at evaluation, when everything is on. Two ways fix this, and the paper notes they
are equivalent:

| Approach | During training | At evaluation | Who uses it |
| --- | --- | --- | --- |
| Scale at test time | Kept outputs pass unchanged | Weights are multiplied by the keep probability | The original paper |
| Scale at training time ("inverted") | Kept outputs are divided by the keep probability | Nothing changes; the layer passes everything through | PyTorch `torch.nn.Dropout` |

**Two conventions for the letter p.** This is the most common source of confusion:

- In **Srivastava et al.**, p is the probability of **keeping** a unit. p = 1 means no dropout.
- In **PyTorch** and in the configuration files of Hugging Face Transformers, the number is the
  probability of **dropping** (zeroing). 0 means no dropout.
- This page uses the second convention, so its numbers can be typed into a configuration directly.

**What the original paper recommends** (converted to the share dropped):

- **Hidden units:** drop 50%. The paper says a keep probability of 0.5 "seems to be close to
  optimal for a wide range of networks and tasks"; its appendix gives 0.5 to 0.8 as typical keep
  probabilities, which is dropping 20% to 50%.
- **Input units:** drop less. For real-valued inputs such as image patches or speech frames a
  typical keep probability is 0.8, which is dropping 20%.
- In their MNIST sweep with fixed layer sizes, the test error was flat for keep probabilities from
  0.4 to 0.8 (dropping 20% to 60%) and rose on both sides.

**What published models used:**

| Model | Dropout (share dropped) | Notes from the source |
| --- | --- | --- |
| Original Transformer, base model | 0.1 | Applied to each sub-layer output and to the embeddings |
| Original Transformer, big model | 0.3 for English-to-German, 0.1 for English-to-French | |
| BERT | 0.1 | "on all layers", kept at 0.1 for fine-tuning |
| Vision Transformer, JFT-300M | 0.0 | Its largest training set |
| Vision Transformer, ImageNet-21k and ImageNet | 0.1 | Smaller training sets |

The Transformer paper also tested its base model without dropout: on its English-to-German
development set, removing dropout (0.1 to 0.0) raised the perplexity from 4.92 to 5.77 and lowered
the BLEU translation score from 25.8 to 24.6. The authors conclude that dropout is "very helpful in
avoiding over-fitting".

**In library defaults:** PyTorch's `torch.nn.Dropout` defaults to 0.5. Hugging Face's `BertConfig`
defaults to 0.1 (`hidden_dropout_prob` and `attention_probs_dropout_prob`). Its `LlamaConfig` has a
single dropout setting, `attention_dropout`, which defaults to 0.0.

## Calculation

Two things are calculated: **what dropout does to one output**, and **how wide a layer should be**
when dropout is on.

### 1. One output during training (PyTorch's inverted dropout)

In words: with probability p the output becomes zero; otherwise it is divided by the probability
of keeping it, 1 − p.

$$
\tilde{a} =
\begin{cases}
0 & \text{with probability } p \\[4pt]
\dfrac{a}{1 - p} & \text{with probability } 1 - p
\end{cases}
$$

| Symbol | Meaning |
| --- | --- |
| $a$ | the output of a unit before dropout |
| $\tilde{a}$ | the output after dropout, passed to the next layer |
| $p$ | the dropout setting: the probability that the output is zeroed |
| $1 - p$ | the probability that the output is kept |

Steps:

1. Draw a random number to decide whether the output is kept (probability $1 - p$).
2. If it is dropped, the output is $0$.
3. If it is kept, divide it by $1 - p$.
4. At evaluation, skip all of this: $\tilde{a} = a$.

**Why the average stays the same.** The expected (average) output is the chance of each outcome
times its value, added up:

$$
p \times 0 + (1 - p) \times \frac{a}{1 - p} = a
$$

### 2. How wide a layer should be

In words: if a layer has n units and each is kept with probability 1 − p, then on average
n × (1 − p) of them are present at a step. Srivastava et al. suggest that if n units are right
without dropout, a dropout layer should have at least n divided by the keep probability.

$$
\text{expected units present} = n \times (1 - p)
\qquad
n_\text{dropout} \ge \frac{n}{1 - p}
$$

| Symbol | Meaning |
| --- | --- |
| $n$ | the number of units the layer would need without dropout |
| $n_\text{dropout}$ | the number of units to give the layer when dropout is on |

## Worked Example

### Part A: one training step, by hand

A layer with five outputs: **2, 4, 1, 3, 5**. Dropout setting $p$ = **0.2**, the share the paper
drops from inputs. The random draws in this example happen to drop the second output only.

1. Keep probability: $1 - 0.2 = 0.8$.
2. Scale factor for kept outputs: $1 / 0.8 = 1.25$.
3. Kept outputs, scaled: $2 \times 1.25 = 2.5$, $1 \times 1.25 = 1.25$, $3 \times 1.25 = 3.75$,
   $5 \times 1.25 = 6.25$.
4. The dropped output becomes $0$.
5. Result: **2.5, 0, 1.25, 3.75, 6.25**. Its sum is 13.75, against 15 before dropout; another step
   draws a different pattern.

### Part B: the average output is unchanged

Take the second output, $a = 4$, over many steps.

1. With probability 0.2 it is dropped: contribution $0.2 \times 0 = 0$.
2. With probability 0.8 it is kept and scaled to $4 \times 1.25 = 5$: contribution
   $0.8 \times 5 = 4$.
3. Average: $0 + 4 = 4$, the same as the output without dropout.

This is why PyTorch can pass the outputs through unchanged at evaluation.

### Part C: sizing a layer

A layer that needs **256** units without dropout, trained with $p$ = **0.5** (the paper's value
for hidden units).

1. Width with dropout: $256 / (1 - 0.5) = 256 / 0.5 = 512$ units.
2. Units present at a step, on average: $512 \times (1 - 0.5) = 256$.

### Part D: converting the paper's numbers to PyTorch

1. The paper keeps input units with probability **0.8**: PyTorch $p = 1 - 0.8 = 0.2$.
2. The paper keeps hidden units with probability **0.5**: PyTorch $p = 1 - 0.5 = 0.5$.

**What this means:** dropout zeroes a random share of outputs and scales the rest so that the
average is unchanged; a higher setting removes more units per step, so the layer needs to be wider
to keep the same number at work.

## What To Watch

- **Training error and validation error side by side.** Dropout is meant to close the gap between
  them. Srivastava et al. show that too much dropout on a layer of fixed size raises the training
  error as well, which is underfitting.
- **Which convention a value uses.** A "p" from the original paper is a keep probability; a value
  in PyTorch or a Hugging Face configuration is a drop probability.
- **Training versus evaluation mode.** In PyTorch, dropout acts only in training mode;
  `model.eval()` turns it off.
- **Every dropout setting of the model.** Configurations can hold several: `BertConfig` has
  `hidden_dropout_prob` and `attention_probs_dropout_prob`; `LlamaConfig` has `attention_dropout`.
- **The size of the data set.** The Vision Transformer used no dropout on its largest data set and
  0.1 on the smaller ones; Srivastava et al. found that the gain from dropout depends on how much
  data there is.
- **Training time.** Srivastava et al. report that dropout networks typically take 2 to 3 times
  longer to train.
