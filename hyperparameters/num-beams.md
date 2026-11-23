---
id: hyperparameter/num-beams
type: hyperparameter
slug: num-beams
schema_version: 3.1
title: Number of Beams
name: num_beams
context: inference
priority: 7
importance: optional
goals:
- inference
models:
- llm
range: 1 — 12
controls: 'How many candidate texts a language model keeps writing side by side before it returns the one with the highest overall probability: 1 keeps a single text, higher values keep several.'
effects:
  decrease:
  - Fewer candidate texts are kept at each step
  - Less computation and memory per generated word
  - 'At 1 there is no beam search: one text is written word by word'
  increase:
  - More candidate texts are kept at each step
  - A text with a higher overall probability can be found
  - The model runs on that many texts at every step
  - Past a point, quality has been measured to drop
scale:
  min: 1
  max: 100
  typical_min: 1
  typical_max: 12
  spacing: log
typical_values:
- setting: 'Hugging Face Transformers: used when the model''s generation_config.json sets no value (no beam search)'
  value: '1'
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py
- setting: Original Transformer (Vaswani et al., 2017), machine translation, with length penalty 0.6
  value: '4'
  source: https://arxiv.org/abs/1706.03762
- setting: 'facebook/bart-large-cnn: value in the model''s own generation_config.json'
  value: '4'
  source: https://huggingface.co/facebook/bart-large-cnn/blob/main/generation_config.json
- setting: 'Helsinki-NLP/opus-mt-en-de: value in the model''s own generation_config.json'
  value: '4'
  source: https://huggingface.co/Helsinki-NLP/opus-mt-en-de/blob/main/generation_config.json
- setting: 'google/pegasus-xsum: value in the model''s own generation_config.json'
  value: '8'
  source: https://huggingface.co/google/pegasus-xsum/blob/main/generation_config.json
- setting: 'Sutskever et al. (2014), machine translation with LSTMs: the largest beam in the paper''s results (it also reports 1 and 2)'
  value: '12'
  source: https://arxiv.org/abs/1409.3215
mistakes:
- mistake: Using beam search for open-ended writing such as stories or chat
  why: Beam search looks for the **most probable** text, and for open-ended writing the most probable text is not the best one. Holtzman et al. find that decoding which maximizes probability gives text that is "bland, incoherent, or gets stuck in repetitive loops", and their example with a beam of 32 ends in such a loop. Fan et al. report that beam search "tends to produce common phrases and repetitive text" in story generation.
  fix: Keep `num_beams=1` and sample (`do_sample=True` with `top_p` or `top_k`) for open-ended text. The Transformers documentation describes beam search as "best suited for input-grounded tasks", such as describing an image or speech recognition.
- mistake: Assuming more beams always give a better result
  why: 'Koehn and Knowles measured translation quality at beam sizes from 1 to 1,000 for eight language pairs. Quality rose at first and then **fell**: "in almost all cases, worse translations are found beyond an optimal beam size setting". They name the main cause: "shorter translations under wider beams".'
  fix: Start from the value the model ships with (4 for bart-large-cnn and opus-mt-en-de) and measure before going higher. Sutskever et al. found that "a beam of size 2 provides most of the benefits of beam search" for their system.
- mistake: Raising num_beams without budgeting for the cost
  why: Every beam is a text of its own that the model has to continue. In Transformers the batch that goes through the model at each step holds **`num_beams` sequences per input**, so the work per generated token grows with the number of beams.
  fix: Treat `num_beams=4` as about four texts generated at once, and check time and memory before using it on long outputs or large batches.
- mistake: Asking for more returned texts than there are beams
  why: Beam search ends with `num_beams` finished candidates and can return only those. Transformers raises a `ValueError` when `num_return_sequences` is larger than `num_beams`.
  fix: Set `num_beams` at least as high as `num_return_sequences`.
visual:
  kind: beams
  prompt: The weather is
  num_beams: 2
  tree:
  - token: very
    probability: 0.5
    next:
    - token: nice
      probability: 0.4
    - token: cold
      probability: 0.3
    - token: hot
      probability: 0.3
  - token: sunny
    probability: 0.4
    next:
    - token: today
      probability: 0.9
    - token: and
      probability: 0.1
  - token: bad
    probability: 0.1
    next:
    - token: today
      probability: 0.6
    - token: again
      probability: 0.4
  caption: 'Two steps of beam search with 2 beams after "The weather is". Step 1: the probability of each first word; the 2 most likely are kept. Step 2: the probability of each pair of words built on them (the two steps multiplied). The probabilities are made up for this figure, not taken from a model.'
tags:
- inference
- decoding
- beam-search
updated: '2026-11-23'
related:
- hyperparameter/do-sample
- hyperparameter/max-new-tokens
- hyperparameter/min-new-tokens
sources:
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/generation/utils.py
  title: 'Hugging Face Transformers source: generation/utils.py (beam search)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py
  title: 'Hugging Face Transformers source: generation/configuration_utils.py (GenerationConfig: decoding modes, defaults, checks)'
  trust: 0.9
- url: https://huggingface.co/docs/transformers/generation_strategies
  title: 'Hugging Face Transformers documentation: Generation strategies'
  trust: 0.9
- url: https://arxiv.org/abs/1409.3215
  title: Sequence to Sequence Learning with Neural Networks (Sutskever, Vinyals, Le, 2014)
  trust: 0.9
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.9
- url: https://arxiv.org/abs/1706.03872
  title: Six Challenges for Neural Machine Translation (Koehn and Knowles, 2017)
  trust: 0.9
- url: https://arxiv.org/abs/1904.09751
  title: The Curious Case of Neural Text Degeneration (Holtzman et al., 2019)
  trust: 0.9
- url: https://arxiv.org/abs/1805.04833
  title: Hierarchical Neural Story Generation (Fan, Lewis, Dauphin, 2018)
  trust: 0.9
- url: https://huggingface.co/facebook/bart-large-cnn/blob/main/generation_config.json
  title: 'facebook/bart-large-cnn: generation_config.json'
  trust: 0.9
- url: https://huggingface.co/Helsinki-NLP/opus-mt-en-de/blob/main/generation_config.json
  title: 'Helsinki-NLP/opus-mt-en-de: generation_config.json'
  trust: 0.9
- url: https://huggingface.co/google/pegasus-xsum/blob/main/generation_config.json
  title: 'google/pegasus-xsum: generation_config.json'
  trust: 0.9
---

## Definition

**`num_beams`** is the number of candidate texts that **beam search** keeps alive while a model
writes.

- A **token** is the unit of text a language model writes: a word or a piece of a word. This
  page says "word" where the difference does not matter.
- A model writes one token at a time. For each next token it gives every token it knows a
  **probability**: a share between 0 and 1, all shares adding up to 1.
- The **probability of a whole text** is the probabilities of its tokens multiplied together.
- **Greedy decoding** (`num_beams=1`, no sampling) takes the most likely token at each step and
  never looks back.
- **Beam search** keeps the `num_beams` most probable **unfinished texts** (the **beams**) at
  every step, extends each of them, and keeps the best `num_beams` again. At the end it returns
  the finished text with the best score.

Sutskever, Vinyals and Le describe it for translation: the decoder "maintains a small number B
of partial hypotheses", extends each with every possible word, and discards "all but the B most
likely".

**Analogy:** finding a route through a maze by sending several scouts instead of one. At each
junction every scout tries every passage, and only the few scouts on the most promising paths
carry on. One scout alone takes the passage that looks best right now and may walk into a dead
end. The analogy shows the idea only; the exact rule is under Calculation.

## How It Works

**The figure shows two steps of beam search with 2 beams**, on a small table of made-up
probabilities.

How to read the figure:

- **First panel, step 1:** three candidate first words after "The weather is". The two most
  likely, "very" and "sunny", are **kept**. "bad" is greyed out: it is dropped, and nothing that
  could follow it is ever looked at.
- **Second panel, step 2:** every pair of words that starts with a kept word. Each bar is the
  probability of the **pair**: the first word's probability times the second word's.
- **Greedy decoding** took "very" (the most likely first word) and then "nice" (the most likely
  word after it): 0.200.
- **Beam search** also followed "sunny", and found "sunny today" at 0.360: a more probable pair
  that starts with a less probable word.

What happens step by step (Hugging Face Transformers):

1. The model computes the probabilities of the next token **for every beam**.
2. Each beam's score so far is combined with each possible next token. That gives
   `num_beams` × (vocabulary size) candidate texts.
3. The candidates are **ranked by score**, and the best unfinished ones become the new beams.
4. A candidate that ends with the **end-of-sequence token** (the token that says "the text is
   finished") leaves the beams and joins the finished texts.
5. Steps 1 to 4 repeat until the search stops. The finished text with the best score is returned.

What the setting buys and what it costs:

- **It can find a more probable text.** The Transformers documentation puts it this way: beam
  search "can 'look ahead' and pick a sequence with a higher probability overall even if the
  initial tokens have a lower probability".
- **It is still approximate.** A text whose first word was dropped can never be found.
  Sutskever et al. call the decoder "approximate" for this reason.
- **It costs computation.** The model continues `num_beams` texts at every step instead of one.

Where it is used:

- The published values in the table (Sutskever et al., Vaswani et al.) come from **machine
  translation**, a task where the output is tightly tied to an input.
- For **open-ended** text, Holtzman et al. and Fan et al. both report that beam search gives
  repetitive, generic output (see Common mistakes).

`num_beams` works together with `do_sample`: with `do_sample=False` (the fallback) the best
candidates are taken; with `do_sample=True` they are drawn at random according to their scores.

## Calculation

In words: the score of a text is the sum of the **logarithms** of its token probabilities. Beam
search keeps the texts with the highest sums. When a text is finished, its sum is divided by its
length, so that a longer text is not marked down only for being longer.

$$
\text{sum}(y_1 \dots y_n) = \sum_{t=1}^{n} \ln p(y_t)
$$

$$
\text{final score} = \frac{\text{sum}(y_1 \dots y_n)}{n^{\alpha}}
$$

| Symbol | Meaning |
| --- | --- |
| $y_1 \dots y_n$ | the generated tokens of one candidate text |
| $n$ | how many tokens were generated |
| $p(y_t)$ | the probability the model gave token $y_t$ at step $t$, given the text before it |
| $\ln$ | the natural logarithm; the logarithm of a number between 0 and 1 is negative |
| $\sum_{t=1}^{n}$ | "add up over every generated token" |
| $\alpha$ | the `length_penalty` setting; its fallback value in Transformers is 1.0 |

Why logarithms: adding logarithms ranks texts in the same order as multiplying probabilities,
and it avoids multiplying many small numbers. A sum **closer to zero** means a more probable
text.

Steps:

1. **Start** with one beam: the prompt, with a sum of 0.
2. **Extend** every beam with every possible next token, and add $\ln p$ of that token to the
   beam's sum.
3. **Keep** the `num_beams` unfinished candidates with the highest sums.
4. **Move** any candidate that just ended into the finished set, with its sum divided by
   $n^{\alpha}$.
5. **Repeat** from step 2 until the search stops, then return the finished text with the highest
   final score.

This is what the beam search of Hugging Face Transformers does. Vaswani et al. used a beam size
of 4 with $\alpha = 0.6$ for translation.

## Worked Example

The numbers of the figure: `num_beams = 2`, and the made-up probabilities below for the two
words after "The weather is". A real model gives a probability to every token in its vocabulary.

| First word | Probability | Words that can follow, with their probabilities |
| --- | --- | --- |
| very | 0.5 | nice 0.4, cold 0.3, hot 0.3 |
| sunny | 0.4 | today 0.9, and 0.1 |
| bad | 0.1 | today 0.6, again 0.4 |

### Greedy decoding (num_beams = 1)

1. **Step 1:** the most likely first word is "very" (0.5).
2. **Step 2:** the most likely word after "very" is "nice" (0.4).
3. **Result:** "very nice", with probability 0.5 × 0.4 = **0.20**.

### Beam search with 2 beams

1. **Step 1, rank the first words:** very 0.5, sunny 0.4, bad 0.1.
2. **Keep the best two:** "very" and "sunny". "bad" is dropped.
3. **Step 2, extend both beams** and multiply:
   - very nice: 0.5 × 0.4 = **0.20**
   - very cold: 0.5 × 0.3 = **0.15**
   - very hot: 0.5 × 0.3 = **0.15**
   - sunny today: 0.4 × 0.9 = **0.36**
   - sunny and: 0.4 × 0.1 = **0.04**
4. **Keep the best two:** "sunny today" (0.36) and "very nice" (0.20).
5. **Result:** "sunny today", with probability **0.36**.

### The same with logarithms, as the library computes it

Values used, rounded: $\ln 0.5 \approx -0.693$, $\ln 0.4 \approx -0.916$,
$\ln 0.9 \approx -0.105$.

1. very nice: -0.693 + (-0.916) = **-1.609**.
2. sunny today: -0.916 + (-0.105) = **-1.021**.
3. -1.021 is closer to zero than -1.609, so "sunny today" ranks first: the same order as 0.36
   against 0.20.
4. If both texts ended here, each sum would be divided by its length, 2 tokens, with
   $\alpha = 1$: -1.021 / 2 ≈ **-0.51** and -1.609 / 2 ≈ **-0.80**. The order stays the same.

### Side by side

| Method | Text | Probability |
| --- | --- | --- |
| Greedy decoding (1 beam) | very nice | 0.20 |
| Beam search (2 beams) | sunny today | 0.36 |

**What this means:** greedy decoding committed to "very" because it was the best first word, and
could not recover when nothing likely followed it. Keeping a second beam cost one more text to
follow and found a text almost twice as probable.

### What 2 beams did not look at

"bad" was dropped after step 1, so "bad today" (0.1 × 0.6 = 0.06) was never scored. Here that
loses nothing, because 0.06 is below 0.36. In general a dropped beam can hide the best text; a
larger `num_beams` makes that less likely and costs more.

## What To Watch

- **The value in force.** Transformers uses `num_beams=1` (no beam search) unless the model's
  `generation_config.json` says otherwise. Some models ship a higher value: 4 for
  bart-large-cnn and opus-mt-en-de, 8 for pegasus-xsum.
- **Output length.** Koehn and Knowles trace the quality drop at large beams to shorter outputs,
  and report that normalizing the score by length "alleviates the problem somewhat".
  Transformers divides by the length through `length_penalty`.
- **Repetition and generic phrasing.** These are the failures Holtzman et al. and Fan et al.
  report for beam search on open-ended text.
- **Time and memory.** Both grow with the number of beams, because every beam is continued at
  every step.
- **Settings that only matter with beams.** `length_penalty` and `early_stopping` are ignored
  with `num_beams=1`; Transformers logs a warning that they are "only used in beam-based
  generation modes".
