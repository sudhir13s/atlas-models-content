---
id: hyperparameter/top-k
type: hyperparameter
slug: top-k
schema_version: 2.2
title: "Top-k Sampling"
name: top_k
context: inference
priority: 5
importance: important
goals: [inference]
models: [llm]
range: "10 — 50"
controls: "How many of the most likely next words a language model is allowed to choose from: only the k best-scoring candidates stay in the draw, all others are removed."
effects:
  decrease:
    - "Fewer candidate words stay in the draw"
    - "Output is more focused and more predictable"
    - "Too low, and text turns bland or starts to repeat"
    - "At 1 only the single most likely word is left"
  increase:
    - "More candidate words stay in the draw"
    - "Output is more varied"
    - "Too high, and unsuitable words get picked more often"
    - "At the size of the vocabulary nothing is removed"
scale:
  min: 1
  max: 1000
  typical_min: 10
  typical_max: 50
  spacing: log
typical_values:
  - setting: "Hugging Face Transformers: used when the model's generation_config.json sets no value"
    value: "50"
    source: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py"
  - setting: "Fan et al. (2018), story generation with top-k random sampling"
    value: "10"
    source: "https://arxiv.org/abs/1805.04833"
  - setting: "Holtzman et al. (2019), GPT-2 Large: one of the two top-k settings the paper compares against (the other is 640)"
    value: "40"
    source: "https://arxiv.org/abs/1904.09751"
  - setting: "Qwen2.5-7B-Instruct: value in the model's own generation_config.json (with top_p 0.8 and temperature 0.7)"
    value: "20"
    source: "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct/blob/main/generation_config.json"
  - setting: "Qwen3-8B: value in the model's own generation_config.json (with top_p 0.95 and temperature 0.6)"
    value: "20"
    source: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/generation_config.json"
mistakes:
  - mistake: "Setting top_k while sampling is switched off"
    why: >-
      Top-k only decides **which tokens may be drawn at random**. Hugging Face Transformers
      applies it only when `do_sample=True`; with the default `do_sample=False` the library takes
      the most likely token and the value of `top_k` changes nothing. The library logs a warning
      that the flag "is only used in sample-based generation modes".
    fix: >-
      Pass `do_sample=True` together with `top_k`, or check that the model's
      `generation_config.json` already switches sampling on.
  - mistake: "Assuming there is no top-k filter because none was set"
    why: >-
      Transformers falls back to **`top_k=50`** when neither you nor the model's configuration
      sets a value. Switching sampling on with `do_sample=True` alone therefore already limits the
      draw to the 50 best-scoring tokens.
    fix: >-
      To sample with no top-k filter, pass **`top_k=0`**: the library adds the filter only when the
      value is not 0. Then let `top_p` or the temperature shape the draw.
  - mistake: "Relying on one fixed k for every word of the text"
    why: >-
      The number of sensible next words changes from step to step. Holtzman et al. show both
      cases: sometimes tens or hundreds of words are reasonable, sometimes almost all the
      probability sits on one or two. A **small k** then risks "bland or generic text"; a **large
      k** lets in "inappropriate candidates" whose chance of being picked goes **up** when the
      kept probabilities are rescaled.
    fix: >-
      Use `top_p`, which keeps more or fewer tokens depending on how sure the model is, alone or
      together with a moderate `top_k` (Qwen2.5-7B-Instruct ships `top_k=20` with `top_p=0.8`).
  - mistake: "Expecting exactly k tokens to survive"
    why: >-
      The library removes every token whose score is **below the k-th highest score**. Tokens
      that tie with the k-th one are all kept, so more than k can remain. When `top_p` is also
      set, it runs **after** top-k and can remove more, so fewer than k can remain.
    fix: >-
      Read `top_k` as an upper limit on the candidates in normal cases, not as an exact count.
visual:
  kind: distribution
  tokens: ["blue", "clear", "falling", "green", "loud"]
  logits: [3, 2, 1, 0, -1]
  truncate:
    top_k: 2
  caption: "Five candidate next words for \"The sky is\" with scores 3, 2, 1, 0 and -1, before and after top_k = 2. The scores are made up for this figure, not taken from a model; every probability is computed from them as under Calculation."
tags: [inference, sampling, decoding]
updated: 2026-11-23
related: [hyperparameter/do-sample, hyperparameter/top-p, hyperparameter/temperature]
sources:
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/logits_process.py", title: "Hugging Face Transformers source: logits_process.py (TopKLogitsWarper)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py", title: "Hugging Face Transformers source: generation/configuration_utils.py (GenerationConfig: parameters, defaults, warnings)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/utils.py", title: "Hugging Face Transformers source: generation/utils.py (which filters are applied, and in what order)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1805.04833", title: "Hierarchical Neural Story Generation (Fan, Lewis, Dauphin, 2018)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1904.09751", title: "The Curious Case of Neural Text Degeneration (Holtzman et al., 2019)", trust: 0.9 }
  - { url: "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct/blob/main/generation_config.json", title: "Qwen2.5-7B-Instruct: generation_config.json", trust: 0.9 }
  - { url: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/generation_config.json", title: "Qwen3-8B: generation_config.json", trust: 0.9 }
---

## Definition

**Top-k** is a whole number that limits the next-word draw to the **k highest-scoring
candidates**.

- A **token** is the unit of text a language model writes: a word or a piece of a word. This
  page says "word" where the difference does not matter.
- To write the next token, the model gives **every** token it knows a **score** (the technical
  name is **logit**). A higher score means "fits better here".
- The scores are turned into **probabilities** (shares that add up to 1), and with **sampling**
  one token is drawn at random according to those shares.
- Top-k steps in **before the draw**: it keeps the k tokens with the highest scores and removes
  all the others, so they can no longer be drawn.
- The kept probabilities are then **rescaled** so that they add up to 1 again.

**Analogy:** a raffle in which only the k people holding the most tickets may take part. Everyone
else's tickets are thrown away, and the winner is drawn from the tickets that remain. The analogy
shows the idea only; the exact rule is under Calculation.

Fan, Lewis and Dauphin used this scheme for story generation: at each step they "randomly sample
from the k = 10 most likely candidates".

## How It Works

**The figure shows one set of scores twice**: as the model produced them, and after top-k with
k = 2.

How to read the figure:

- Each bar is one candidate word; its length is the probability that this word is picked.
- **First panel (before):** all five candidates can be picked, including the unlikely "loud".
- **Second panel (after):** only the two best-scoring words are left. The three removed words are greyed
  out and have probability 0.
- The two kept bars are **longer** than before. Their probabilities were divided by their own
  total so that they add up to 1 again; the ratio between them did not change.

What happens step by step when a model writes one token (Hugging Face Transformers, with
`do_sample=True`):

1. The model computes a score for every token.
2. The scores are divided by the **temperature**, if one is set.
3. **Top-k:** every token whose score is below the k-th highest score is removed (its score is
   set to minus infinity).
4. **Top-p**, if set below 1, removes more tokens from what is left.
5. The remaining scores are turned into probabilities, and one token is drawn.

Why removing the unlikely tokens helps:

- Fan et al. give the reason: completely random sampling "can introduce very unlikely words,
  which can damage generation", and restricting the draw to the most likely candidates "reduces
  the risk of these low-probability samples".
- Holtzman et al. call the many unlikely tokens the **"unreliable tail"** of the distribution
  and report that sampling from the full distribution gives incoherent text.

Why one fixed k is a compromise (Holtzman et al.):

| Situation at this step | With a small k | With a large k |
| --- | --- | --- |
| Many words are reasonable (the probabilities are flat) | Reasonable words are cut off; risk of "bland or generic text" | Fine |
| One or two words are clearly right (the probabilities are peaked) | Fine | Unsuitable words stay in, and rescaling raises their chance |

The same k is used at every step, so it cannot fit both situations. This is the reason the same
paper proposes top-p, which adapts the number of kept tokens to each step.

## Calculation

In words: find the k-th highest score, remove every token that scores below it, and divide each
remaining probability by the total of the remaining probabilities.

$$
p'_i = \frac{p_i}{\sum_{j \in V_k} p_j} \quad \text{for } i \in V_k, \qquad p'_i = 0 \text{ otherwise}
$$

| Symbol | Meaning |
| --- | --- |
| $k$ | the value of `top_k`, a whole number of at least 1 |
| $V_k$ | the set of kept tokens: every token whose score is not below the k-th highest score |
| $p_i$ | the probability of token $i$ before top-k |
| $\sum_{j \in V_k}$ | "add up over every kept token $j$" |
| $p'_i$ | the probability of token $i$ after top-k |

Steps:

1. **Rank** the tokens by score, highest first.
2. **Find the threshold:** the score of the token in place k.
3. **Remove** every token whose score is below the threshold.
4. **Add up** the probabilities of the kept tokens.
5. **Divide** each kept probability by that total. The results add up to 1.

How the library does it: Hugging Face Transformers sets the removed scores to minus infinity and
then turns the scores into probabilities. That gives exactly the result of steps 4 and 5. The
rescaled form above is how Holtzman et al. write top-k.

Limits of the value in Transformers:

- `top_k` must be a **whole number above 0** when the filter is used; the filter class raises a
  `ValueError` otherwise.
- **`top_k=0`** passed to `generate` means "no top-k filter": the filter is not added.
- A k larger than the vocabulary is lowered to the vocabulary size, which removes nothing.

## Worked Example

The numbers of the figure: five candidate words for "The sky is" with scores **3, 2, 1, 0
and -1**, and `top_k = 2`. The scores are made up so the arithmetic stays small; a real model
scores every token in its vocabulary at once.

Values of $e^x$ used below, rounded: $e^{3} \approx 20.086$, $e^{2} \approx 7.389$,
$e^{1} \approx 2.718$, $e^{0} = 1$, $e^{-1} \approx 0.368$.

### Before top-k

1. Raise $e$ to each score: 20.086, 7.389, 2.718, 1, 0.368.
2. Add up: 20.086 + 7.389 + 2.718 + 1 + 0.368 = 31.561.
3. Divide each by the total:
   - blue: 20.086 / 31.561 ≈ **0.636**
   - clear: 7.389 / 31.561 ≈ **0.234**
   - falling: 2.718 / 31.561 ≈ **0.086**
   - green: 1 / 31.561 ≈ **0.032**
   - loud: 0.368 / 31.561 ≈ **0.012**

### After top_k = 2

1. **Rank** by score: blue (3), clear (2), falling (1), green (0), loud (-1).
2. **Threshold:** the score in place 2 is **2** (clear).
3. **Remove** everything below 2: falling, green and loud.
4. **Add up** the kept probabilities: 0.636 + 0.234 = 0.870.
5. **Divide** by the total:
   - blue: 0.636 / 0.870 ≈ **0.731**
   - clear: 0.234 / 0.870 ≈ **0.269**
   - falling, green, loud: **0**

### Side by side

| Word | Score | Before | After top_k = 2 |
| --- | --- | --- | --- |
| blue | 3 | 0.636 | 0.731 |
| clear | 2 | 0.234 | 0.269 |
| falling | 1 | 0.086 | 0 |
| green | 0 | 0.032 | 0 |
| loud | -1 | 0.012 | 0 |

**What this means:** out of 1,000 draws, about 130 would have gone to "falling", "green" or
"loud" before (86 + 32 + 12). After top-k none do: "blue" is picked about 731 times and "clear"
about 269 times.

### Two other values of k

- **k = 1:** only "blue" is kept, with probability 1. Every draw gives the most likely word.
- **k = 5:** all five words are kept and nothing changes. Any k at or above the number of
  candidates does nothing.

## What To Watch

- **Whether sampling is on.** In Hugging Face Transformers, `top_k` does nothing unless
  `do_sample=True`.
- **The value in force.** Transformers reads `top_k` from the model's `generation_config.json`
  and uses **50** when that file sets none. Qwen2.5-7B-Instruct and Qwen3-8B both ship 20.
- **Repetition at low values.** Holtzman et al. find that all sampling methods "face repetition
  issues when their tuning parameters are set too low", because the draw then behaves like
  always taking the most likely token.
- **How high is unusual.** The same paper notes that "values of k greater than 100 are rarely
  used in practice". The Transformers source carries the note that LLMs use `top_k` "in the 5-50
  range".
- **The other sampling settings.** Temperature is applied before top-k and `top_p` after it, so
  judge the three together.
