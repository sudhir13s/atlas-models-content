---
id: hyperparameter/temperature
type: hyperparameter
slug: temperature
schema_version: 2.2
title: "Temperature"
name: temperature
context: inference
priority: 1
importance: critical
goals: [inference]
models: [llm]
range: "0.6 — 1.0"
controls: "How adventurous a language model is when it picks the next word: low values make it stay with the most likely word, high values give unlikely words a real chance."
effects:
  decrease:
    - "The most likely words take a larger share of the probability"
    - "Output is more focused and more repeatable"
    - "Less variety from one run to the next"
    - "Near zero: always the single most likely word"
  increase:
    - "Probability spreads out to less likely words"
    - "Output is more varied and more random"
    - "Unlikely words get picked more often, and text can lose its thread"
    - "Very high: every word becomes almost equally likely"
scale:
  min: 0
  max: 2
  typical_min: 0.6
  typical_max: 1
  spacing: linear
typical_values:
  - setting: "Hugging Face Transformers: used when the model's generation_config.json sets no value"
    value: "1.0"
    source: "https://huggingface.co/docs/transformers/main_classes/text_generation"
  - setting: "OpenAI Chat Completions API: default (the API accepts 0 to 2)"
    value: "1"
    source: "https://developers.openai.com/api/reference/resources/chat"
  - setting: "Qwen2.5-7B-Instruct: value in the model's own generation_config.json (with top_p 0.8 and top_k 20)"
    value: "0.7"
    source: "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct/blob/main/generation_config.json"
  - setting: "DeepSeek-R1: recommendation on the model card"
    value: "0.5 to 0.7, with 0.6 recommended"
    source: "https://huggingface.co/deepseek-ai/DeepSeek-R1"
mistakes:
  - mistake: "Setting a temperature while sampling is switched off"
    why: >-
      Temperature only matters when the model **draws** the next token at random. In Hugging Face
      Transformers that happens only with `do_sample=True`; otherwise the library uses greedy
      decoding (always the most likely token) and, as its documentation warns, the temperature
      value **has no effect**.
    fix: >-
      Pass `do_sample=True` together with the temperature, or check that the model's
      `generation_config.json` already sets it.
  - mistake: "Passing temperature=0 to Transformers to get the same answer every time"
    why: >-
      The scores are **divided** by the temperature, and nothing can be divided by zero. The
      library requires a strictly positive number and raises a `ValueError` for 0.
    fix: >-
      Set `do_sample=False`. That is greedy decoding, which is what a temperature of zero would
      mean. Hosted APIs differ: the OpenAI API accepts 0 as a value.
  - mistake: "Pushing temperature and top_p to extremes together"
    why: >-
      Both settings change the same list of probabilities. In Transformers the temperature
      reshapes it first, then `top_k` and `top_p` cut off the unlikely end of the reshaped list,
      so the effect of one depends on the other.
    fix: >-
      Change one and hold the other still. The OpenAI documentation gives this advice for its
      API: alter temperature or `top_p`, but not both.
  - mistake: "Copying a temperature from one model or service to another"
    why: >-
      Defaults and limits differ. Transformers falls back to 1.0, but Qwen2.5-7B-Instruct ships
      with 0.7 in its own configuration, and the DeepSeek-R1 card asks for 0.5 to 0.7 to prevent
      endless repetitions or incoherent outputs. The OpenAI API accepts 0 to 2.
    fix: >-
      Read the model's `generation_config.json` and model card first, and treat a value taken
      from elsewhere as something to test.
visual:
  kind: distribution
  tokens: ["blue", "clear", "falling"]
  logits: [2, 1, 0]
  temperatures: [0.5, 1, 2]
  caption: "Three candidate next words for \"The sky is\" with scores 2, 1 and 0, and the probability each one gets at three temperatures. The scores are made up for this figure, not taken from a model; the probabilities are computed from them with the formula under Calculation."
tags: [inference, sampling, decoding]
updated: 2026-11-22
related: [hyperparameter/do-sample, hyperparameter/top-p, hyperparameter/top-k]
sources:
  - { url: "https://huggingface.co/docs/transformers/main_classes/text_generation", title: "Hugging Face Transformers documentation: Generation (GenerationConfig)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/logits_process.py", title: "Hugging Face Transformers source: logits_process.py (TemperatureLogitsWarper)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/utils.py", title: "Hugging Face Transformers source: generation/utils.py (order of the sampling steps)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1503.02531", title: "Distilling the Knowledge in a Neural Network (Hinton, Vinyals, Dean, 2015)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1904.09751", title: "The Curious Case of Neural Text Degeneration (Holtzman et al., 2019)", trust: 0.9 }
  - { url: "https://developers.openai.com/api/reference/resources/chat", title: "OpenAI API reference: Chat Completions", trust: 0.85 }
  - { url: "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct/blob/main/generation_config.json", title: "Qwen2.5-7B-Instruct: generation_config.json", trust: 0.9 }
  - { url: "https://huggingface.co/deepseek-ai/DeepSeek-R1", title: "DeepSeek-R1 model card", trust: 0.9 }
---

## Definition

**Temperature** is a number that the model's scores are divided by before the next token is
chosen.

- A **token** is the unit of text a language model writes: a word or a piece of a word. This
  page says "word" where the difference does not matter.
- To write the next token, the model gives **every** token it knows a **score** (the technical
  name is **logit**). A higher score means "fits better here".
- The scores are turned into **probabilities** (shares that add up to 1), and one token is
  drawn at random according to those shares.
- Temperature changes **how the scores become probabilities**. It does not change the scores,
  and it does not make the model know more or less.

**Analogy:** a lottery in which better-fitting words hold more tickets. A low temperature hands
nearly all the tickets to the favourite; a high temperature shares them out more evenly. The
analogy shows the direction only; the exact rule is under Calculation.

## How It Works

**The figure shows one set of scores turned into probabilities three times**, once per
temperature. Nothing about the model or its scores differs between the three panels.

How to read the figure:

- Each panel is one temperature. Each bar is one candidate word; its length is the probability
  that this word is picked.
- **At 0.5** the favourite ("blue") takes most of the probability and the least likely word
  almost none.
- **At 1** the scores are used as they are. This is the model's own distribution.
- **At 2** the bars move toward each other: the favourite loses share and the least likely word
  gains.
- The **order never changes**. Temperature makes the gaps between the words wider or narrower;
  it never puts a lower-scored word ahead of a higher-scored one.

What happens step by step when a model writes one token:

1. The model computes a score for every token.
2. Every score is **divided by the temperature**.
3. The divided scores are turned into probabilities with a function called **softmax**
   (explained under Calculation).
4. One token is drawn at random with those probabilities, and the process repeats for the
   next token.

Why a division has this effect:

- Dividing by a number **below 1** multiplies every **gap** between two scores (dividing by 0.5
  doubles it). Softmax rewards gaps strongly, and the top word pulls ahead.
- Dividing by a number **above 1** shrinks the gaps, and the probabilities move closer together.
- Hinton, Vinyals and Dean put it this way: the temperature is normally 1, and a higher value
  produces a softer probability distribution.

What it means for the text:

- Holtzman et al. report the trade-off: **lowering** the temperature improves the quality of the
  generated text but **reduces its diversity**.
- The same paper explains the risk at the other end. Sampling straight from the model's full
  distribution gave text that was incoherent and almost unrelated to the context, and the paper
  blames the many unlikely tokens, which it calls the "unreliable tail".
- Never sampling at all is not the answer either: the paper finds that decoding which always
  goes for the most probable text causes repetition and overly generic language.

## Calculation

In words: divide each score by the temperature, raise the constant $e$ (about 2.718) to each
result, then divide each of those by their total so that the shares add up to 1. The last two
operations together are the **softmax** function.

$$
p_i = \frac{e^{\,z_i / T}}{\sum_j e^{\,z_j / T}}
$$

| Symbol | Meaning |
| --- | --- |
| $z_i$ | the score (logit) of token $i$ |
| $T$ | the temperature, a number greater than 0 |
| $e$ | the mathematical constant 2.718... |
| $\sum_j$ | "add up over every token $j$" |
| $p_i$ | the probability that token $i$ is picked |

Steps:

1. **Divide** every score by the temperature: $z_i / T$.
2. **Exponentiate:** compute $e$ to the power of each result. This makes every number positive
   and stretches the gaps between them.
3. **Add up** all the numbers from step 2.
4. **Divide** each number from step 2 by the total from step 3. The results are the
   probabilities; they add up to 1.

This is the formula of Hinton et al. (2015) and of Holtzman et al. (2019). The Hugging Face
Transformers code does step 1 as `scores / temperature`.

## Worked Example

The numbers of the figure: three candidate words for "The sky is" with scores **2, 1 and 0**.
The scores are made up so the arithmetic stays small; a real model scores every token in its
vocabulary at once.

Values of $e^x$ used below, rounded: $e^0 = 1$, $e^{0.5} \approx 1.649$, $e^1 \approx 2.718$,
$e^2 \approx 7.389$, $e^4 \approx 54.598$.

### At temperature 1 (scores unchanged)

1. Divide by 1: 2, 1, 0.
2. Exponentiate: $e^2 \approx 7.389$, $e^1 \approx 2.718$, $e^0 = 1$.
3. Add up: 7.389 + 2.718 + 1 = 11.107.
4. Divide by the total:
   - blue: 7.389 / 11.107 ≈ **0.665**
   - clear: 2.718 / 11.107 ≈ **0.245**
   - falling: 1 / 11.107 ≈ **0.090**

### At temperature 0.5 (lower)

1. Divide by 0.5 (the same as doubling): 4, 2, 0.
2. Exponentiate: $e^4 \approx 54.598$, $e^2 \approx 7.389$, $e^0 = 1$.
3. Add up: 54.598 + 7.389 + 1 = 62.987.
4. Divide by the total:
   - blue: 54.598 / 62.987 ≈ **0.867**
   - clear: 7.389 / 62.987 ≈ **0.117**
   - falling: 1 / 62.987 ≈ **0.016**

### At temperature 2 (higher)

1. Divide by 2: 1, 0.5, 0.
2. Exponentiate: $e^1 \approx 2.718$, $e^{0.5} \approx 1.649$, $e^0 = 1$.
3. Add up: 2.718 + 1.649 + 1 = 5.367.
4. Divide by the total:
   - blue: 2.718 / 5.367 ≈ **0.506**
   - clear: 1.649 / 5.367 ≈ **0.307**
   - falling: 1 / 5.367 ≈ **0.186**

### Side by side

| Word | Score | T = 0.5 | T = 1 | T = 2 |
| --- | --- | --- | --- | --- |
| blue | 2 | 0.867 | 0.665 | 0.506 |
| clear | 1 | 0.117 | 0.245 | 0.307 |
| falling | 0 | 0.016 | 0.090 | 0.186 |

**What this means:** out of 1,000 draws, "falling" would be picked about 16 times at
temperature 0.5, about 90 times at 1 and about 186 times at 2. The model's opinion of the three
words is the same in every column; only the willingness to pick the unlikely one changed.

### The two extremes

- **Very low, T = 0.1:** the divided scores are 20, 10 and 0. $e^{20}$ is about 485 million and
  $e^{10}$ about 22 thousand, so "blue" gets about 0.99995 of the probability. In practice the top
  word is always picked.
- **Very high, T = 100:** the divided scores are 0.02, 0.01 and 0. The exponentials are about
  1.020, 1.010 and 1, so the probabilities are about 0.337, 0.333 and 0.330: almost equal.

## What To Watch

- **Whether sampling is on.** In Hugging Face Transformers, temperature does nothing unless
  `do_sample=True`.
- **The value the model ships with.** Transformers reads the temperature from the model's
  `generation_config.json` and uses 1.0 only when that file sets none.
- **Repetition or loss of coherence.** The DeepSeek-R1 card names both as the reason for its
  recommended range: endless repetitions or incoherent outputs.
- **The other sampling settings.** `top_k` and `top_p` are applied after the temperature and
  remove the unlikely end of the list, so judge the three together.
- **The limits of the service.** The OpenAI API accepts 0 to 2; Transformers accepts any number
  above 0 and rejects 0 itself.
