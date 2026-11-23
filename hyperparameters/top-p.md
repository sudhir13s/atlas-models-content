---
id: hyperparameter/top-p
type: hyperparameter
slug: top-p
schema_version: 2.2
title: "Top-p (Nucleus Sampling)"
name: top_p
context: inference
priority: 2
importance: critical
goals: [inference]
models: [llm]
range: "0.8 — 1.0"
controls: "How much of the total probability a language model may choose its next word from: the most likely words are kept until their probabilities add up to p, and the rest are removed."
effects:
  decrease:
    - "Fewer candidate words stay in the draw"
    - "Output is more focused and more predictable"
    - "Too low, and text starts to repeat"
    - "Near 0 only the single most likely word is left"
  increase:
    - "More candidate words stay in the draw"
    - "Output is more varied"
    - "More of the unlikely words can be picked"
    - "At 1.0 nothing is removed"
scale:
  min: 0
  max: 1
  typical_min: 0.8
  typical_max: 1
  spacing: linear
typical_values:
  - setting: "Hugging Face Transformers: used when the model's generation_config.json sets no value (no top-p filter)"
    value: "1.0"
    source: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py"
  - setting: "OpenAI Chat Completions API: default (the API accepts 0 to 1)"
    value: "1"
    source: "https://developers.openai.com/api/reference/resources/chat"
  - setting: "Holtzman et al. (2019), GPT-2 Large: the nucleus sampling setting of the paper's comparison tables"
    value: "0.95"
    source: "https://arxiv.org/abs/1904.09751"
  - setting: "DeepSeek-R1: value in the model's own generation_config.json (with temperature 0.6)"
    value: "0.95"
    source: "https://huggingface.co/deepseek-ai/DeepSeek-R1/blob/main/generation_config.json"
  - setting: "Qwen2.5-7B-Instruct: value in the model's own generation_config.json (with top_k 20 and temperature 0.7)"
    value: "0.8"
    source: "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct/blob/main/generation_config.json"
mistakes:
  - mistake: "Setting top_p while sampling is switched off"
    why: >-
      Top-p only decides **which tokens may be drawn at random**. Hugging Face Transformers
      applies it only when `do_sample=True`; with the default `do_sample=False` the library takes
      the most likely token and `top_p` changes nothing. The library logs a warning that the flag
      "is only used in sample-based generation modes".
    fix: >-
      Pass `do_sample=True` together with `top_p`, or check that the model's
      `generation_config.json` already switches sampling on.
  - mistake: "Reading top_p = 0.9 as 'the kept words hold exactly 90% of the probability'"
    why: >-
      Tokens are kept **up to and including** the one that carries the running total to p. The
      kept set therefore holds **at least** p, usually more. In the worked example the kept words
      hold 0.957, not 0.9, and a single very likely word can hold far more than p on its own.
    fix: >-
      Read p as a **minimum** share that is always kept. To see what was kept at a step, add up
      the sorted probabilities yourself, as in the worked example.
  - mistake: "Pushing temperature and top_p to extremes together"
    why: >-
      Both act on the same list of probabilities. In Transformers the temperature reshapes the
      list **first** and top-p cuts the reshaped list, so the same `top_p` keeps a different
      number of tokens at a different temperature (part C of the worked example).
    fix: >-
      Change one and hold the other still. The OpenAI documentation gives this advice for its
      API: alter `top_p` or temperature, "but not both".
  - mistake: "Treating 1.0 as the strongest setting"
    why: >-
      The scale runs the other way. At `top_p=1.0` every token stays in the draw: Transformers
      adds the filter only when the value is **below 1.0**. The filter gets stronger as the value
      goes **down**.
    fix: >-
      Lower the value to remove more of the unlikely tokens. Holtzman et al. note that values
      of p "are usually in [0.9, 1)".
visual:
  kind: distribution
  tokens: ["blue", "clear", "falling", "green", "loud"]
  logits: [3, 2, 1, 0, -1]
  truncate:
    top_p: 0.9
  caption: "Five candidate next words for \"The sky is\" with scores 3, 2, 1, 0 and -1, before and after top_p = 0.9. The scores are made up for this figure, not taken from a model; every probability is computed from them as under Calculation."
tags: [inference, sampling, decoding, nucleus]
updated: 2026-11-23
related: [hyperparameter/do-sample, hyperparameter/temperature, hyperparameter/top-k]
sources:
  - { url: "https://arxiv.org/abs/1904.09751", title: "The Curious Case of Neural Text Degeneration (Holtzman et al., 2019)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/logits_process.py", title: "Hugging Face Transformers source: logits_process.py (TopPLogitsWarper)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py", title: "Hugging Face Transformers source: generation/configuration_utils.py (GenerationConfig: parameters, defaults, warnings)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/utils.py", title: "Hugging Face Transformers source: generation/utils.py (which filters are applied, and in what order)", trust: 0.9 }
  - { url: "https://developers.openai.com/api/reference/resources/chat", title: "OpenAI API reference: Chat Completions", trust: 0.85 }
  - { url: "https://huggingface.co/deepseek-ai/DeepSeek-R1/blob/main/generation_config.json", title: "DeepSeek-R1: generation_config.json", trust: 0.9 }
  - { url: "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct/blob/main/generation_config.json", title: "Qwen2.5-7B-Instruct: generation_config.json", trust: 0.9 }
---

## Definition

**Top-p** is a number between 0 and 1. Before the next token is drawn, the most likely tokens
are kept **until their probabilities add up to p**; every other token is removed.

- A **token** is the unit of text a language model writes: a word or a piece of a word. This
  page says "word" where the difference does not matter.
- To write the next token, the model gives **every** token it knows a **score** (the technical
  name is **logit**), and the scores are turned into **probabilities**: shares that add up to 1.
- With **sampling**, one token is drawn at random according to those shares.
- Top-p steps in **before the draw**. The kept group of tokens is called the **nucleus**, which
  is why the method is also called **nucleus sampling**.
- The kept probabilities are then **rescaled** so that they add up to 1 again.

Holtzman et al., who introduced the method, define the nucleus as "the smallest set" of tokens
whose probabilities add up to at least p.

**Analogy:** filling a shortlist. Candidates are called in order of popularity, and the list
closes as soon as the people on it hold the share p of all votes. With one clear favourite the
list is short; with many near-equal candidates it is long. The analogy shows the idea only; the
exact rule is under Calculation.

## How It Works

**The figure shows one set of scores twice**: as the model produced them, and after top-p
with p = 0.9.

How to read the figure:

- Each bar is one candidate word; its length is the probability that this word is picked.
- **First panel (before):** all five candidates can be picked. The line under it adds the probabilities
  up from the most likely word: 0.636, then 0.871, then 0.957.
- The running total **reaches 0.9 at the third word**. That word is still kept; everything
  after it is removed.
- **Second panel (after):** the three kept words, rescaled to add up to 1. The two removed words are
  greyed out and have probability 0.

What happens step by step when a model writes one token (Hugging Face Transformers, with
`do_sample=True`):

1. The model computes a score for every token.
2. The scores are divided by the **temperature**, if one is set.
3. **Top-k**, if active, removes all but the k best-scoring tokens.
4. **Top-p**, if set below 1, removes every token outside the nucleus.
5. The remaining scores are turned into probabilities, and one token is drawn.

Why the number of kept tokens changes from step to step:

- The cut depends on **how the probabilities are spread**, not on a fixed count.
- When the model is **sure**, a few tokens already hold the share p, and the nucleus is small.
- When the model is **unsure**, many tokens are needed to reach p, and the nucleus is large.
- Holtzman et al. describe this as the number of candidates that "rises and falls dynamically"
  with the model's confidence, which a fixed `top_k` cannot do.

Why cut the unlikely tokens at all:

- The same paper finds that sampling from the full distribution gives incoherent text and blames
  the many unlikely tokens, which it calls the **"unreliable tail"**.
- It also finds that always going for the most probable text leads to output that is "bland,
  incoherent, or gets stuck in repetitive loops".
- Nucleus sampling sits between the two: it samples, but only from the part of the distribution
  that holds most of the probability.

## Calculation

In words: sort the tokens from most to least likely, add their probabilities up one at a time,
stop at the first token where the running total reaches p, keep that token and all before it,
and divide each kept probability by the total of the kept probabilities.

$$
p'_i = \frac{p_i}{\sum_{j \in V_p} p_j} \quad \text{for } i \in V_p, \qquad p'_i = 0 \text{ otherwise}
$$

| Symbol | Meaning |
| --- | --- |
| $p$ | the value of `top_p`, between 0 and 1 |
| $V_p$ | the nucleus: the smallest set of the most likely tokens whose probabilities add up to at least $p$ |
| $p_i$ | the probability of token $i$ before top-p |
| $\sum_{j \in V_p}$ | "add up over every token $j$ in the nucleus" |
| $p'_i$ | the probability of token $i$ after top-p |

Steps:

1. **Sort** the tokens by probability, highest first.
2. **Add up** the probabilities one at a time, keeping a running total.
3. **Stop** at the first token where the running total is at least $p$. This token **is kept**.
4. **Remove** every token after it.
5. **Divide** each kept probability by the final running total. The results add up to 1.

How the library does it: the `TopPLogitsWarper` of Hugging Face Transformers reaches the same
set from the other end. It adds the probabilities up starting from the **least** likely token and
removes a token while that running total is still at most $1 - p$. It then sets the removed scores
to minus infinity. At least one token is always kept.

Limits of the value:

- **Transformers:** a value below 0 or above 1 raises a `ValueError`. The filter is added only
  when `top_p` is **below 1.0**; the fallback value 1.0 means no filter.
- **OpenAI Chat Completions API:** accepts 0 to 1, default 1.

## Worked Example

The numbers of the figure: five candidate words for "The sky is" with scores **3, 2, 1, 0
and -1**. The scores are made up so the arithmetic stays small; a real model scores every token
in its vocabulary at once.

Values of $e^x$ used below, rounded: $e^{3} \approx 20.086$, $e^{2} \approx 7.389$,
$e^{1} \approx 2.718$, $e^{0} = 1$, $e^{-1} \approx 0.368$.

### Part A: top_p = 0.9

First the probabilities:

1. Raise $e$ to each score: 20.086, 7.389, 2.718, 1, 0.368.
2. Add up: 20.086 + 7.389 + 2.718 + 1 + 0.368 = 31.561.
3. Divide each by the total: blue **0.636**, clear **0.234**, falling **0.086**, green
   **0.032**, loud **0.012**.

Then the cut:

1. **Sort:** the words are already in order, most likely first.
2. **Running total:**
   - blue: 0.636. Below 0.9, go on.
   - clear: 0.636 + 0.234 = 0.871 (from the unrounded values). Below 0.9, go on.
   - falling: 0.871 + 0.086 = **0.957**. At least 0.9: stop here.
3. **Keep** blue, clear and falling. "falling" is the word that carried the total past 0.9, and
   it stays in.
4. **Remove** green and loud.
5. **Divide** by the kept total, 0.957:
   - blue: 0.636 / 0.957 ≈ **0.665**
   - clear: 0.234 / 0.957 ≈ **0.245**
   - falling: 0.086 / 0.957 ≈ **0.090**

| Word | Score | Before | Running total | After top_p = 0.9 |
| --- | --- | --- | --- | --- |
| blue | 3 | 0.636 | 0.636 | 0.665 |
| clear | 2 | 0.234 | 0.871 | 0.245 |
| falling | 1 | 0.086 | 0.957 | 0.090 |
| green | 0 | 0.032 | removed | 0 |
| loud | -1 | 0.012 | removed | 0 |

**What this means:** out of 1,000 draws, about 44 would have gone to "green" or "loud" before
(32 + 12). After top-p none do. The kept words hold 0.957 of the original probability, more than
the 0.9 that was asked for.

### Part B: the same p on a flat distribution

Five words that the model finds equally fitting: scores **1, 1, 1, 1, 1**.

1. Equal scores give equal probabilities: 1 / 5 = **0.2** each.
2. Running total: 0.2, 0.4, 0.6, 0.8, **1.0**. It reaches 0.9 only at the fifth word.
3. All **five** words are kept.

**What this means:** with the same `top_p = 0.9`, the nucleus held three words in part A and
five here. The setting did not change; the model's confidence did.

### Part C: the same p at a lower temperature

The scores of part A, divided by a temperature of **0.5** before top-p: 6, 4, 2, 0, -2.

Values used: $e^{6} \approx 403.429$, $e^{4} \approx 54.598$, $e^{2} \approx 7.389$,
$e^{0} = 1$, $e^{-2} \approx 0.135$.

1. Add up: 403.429 + 54.598 + 7.389 + 1 + 0.135 = 466.551.
2. Probabilities: blue 403.429 / 466.551 ≈ **0.865**, clear 54.598 / 466.551 ≈ **0.117**.
3. Running total: 0.865, then 0.865 + 0.117 = **0.982**. At least 0.9: stop.
4. Only **two** words are kept: blue and clear.

**What this means:** lowering the temperature made the distribution more peaked, and the same
`top_p = 0.9` now keeps two words instead of three. The two settings are not independent.

## What To Watch

- **Whether sampling is on.** In Hugging Face Transformers, `top_p` does nothing unless
  `do_sample=True`.
- **The value in force.** Transformers reads `top_p` from the model's `generation_config.json`
  and uses 1.0 (no filter) when that file sets none. DeepSeek-R1 ships 0.95 and
  Qwen2.5-7B-Instruct ships 0.8.
- **Repetition at low values.** Holtzman et al. find that all sampling methods "face repetition
  issues when their tuning parameters are set too low", because the draw then behaves like
  always taking the most likely token.
- **The usual range.** The same paper notes that values of p "are usually in [0.9, 1)". The
  Transformers source carries the note that LLMs use `top_p` "in the 0.9-0.95 range".
- **The other sampling settings.** Temperature is applied before top-p, and an active `top_k`
  removes tokens before top-p sees them. In Transformers `top_k` falls back to 50, so it is
  active unless it is set to 0.
