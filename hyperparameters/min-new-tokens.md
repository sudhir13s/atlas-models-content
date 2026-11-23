---
id: hyperparameter/min-new-tokens
type: hyperparameter
slug: min-new-tokens
schema_version: 2.2
title: "Min New Tokens"
name: min_new_tokens
context: inference
priority: 8
importance: optional
goals: [inference]
models: [llm]
range: "0 — 2"
controls: "The smallest number of tokens (words or pieces of words) a language model must write before it is allowed to end its answer: until then, the model's 'I am finished' signal is blocked."
effects:
  decrease:
    - "The model may end its answer sooner"
    - "At 0, or when not set, it stops whenever it chooses to"
  increase:
    - "The model is kept from ending for longer"
    - "Answers that would have ended early continue past their natural end"
    - "Above the maximum length, the maximum wins and the text is cut there"
scale:
  min: 0
  max: 100
  spacing: linear
typical_values:
  - setting: "Hugging Face Transformers: min_new_tokens is not set by default, and the older min_length falls back to 0 (no minimum)"
    value: "0"
    source: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py"
  - setting: "Hugging Face Transformers: the value in the example of the library's own documentation of this filter"
    value: "2"
    source: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/logits_process.py"
  - setting: "facebook/bart-large-cnn: ships the older setting min_length, not min_new_tokens, in its generation_config.json (with max_length 142)"
    value: "min_length 56"
    source: "https://huggingface.co/facebook/bart-large-cnn/blob/main/generation_config.json"
mistakes:
  - mistake: "Using it to get more thorough answers"
    why: >-
      The setting does not ask for more content. It only **forbids the end-of-sequence token**
      for a number of steps. A model that was about to stop must pick its next best token
      instead and carry on from there. The library's documentation says this forces the model
      "to generate beyond its natural ending point".
    fix: >-
      Ask for the length or detail you want in the prompt. Keep `min_new_tokens` for cases where
      an output that ends too early is the problem to solve.
  - mistake: "Setting it higher than the maximum length allows"
    why: >-
      The maximum is a hard stop and the minimum is not. When `min_new_tokens` plus the prompt
      is longer than the maximum length, Transformers warns of "unfeasible length constraints"
      and generation stops at the maximum: the model never gets a step at which it may end, and
      the text is cut.
    fix: >-
      Keep `min_new_tokens` well below `max_new_tokens`, so the model has steps left in which it
      is allowed to finish.
  - mistake: "Confusing it with min_length"
    why: >-
      `min_length` counts the **whole sequence**. For decoder-only models (most LLMs), the
      library states that "the length includes the prompt", so `min_length=20` with a prompt of
      30 tokens has no effect. `min_new_tokens` ignores the prompt. When both are set,
      `min_new_tokens` takes precedence and the library logs a warning.
    fix: >-
      Use `min_new_tokens` when you mean "at least this many generated tokens".
  - mistake: "Expecting it to hold back every way of stopping"
    why: >-
      It blocks one thing: the score of the end-of-sequence token. Generation still ends when
      the maximum length is reached, and the filter is not added at all when no end-of-sequence
      token is defined for the model.
    fix: >-
      Check the maximum length and the model's end-of-sequence token when an output is shorter
      than the minimum you set.
visual:
  kind: distribution
  tokens: ["<end>", "and", "but"]
  logits: [2, 1, 0]
  truncate:
    remove: ["<end>"]
  caption: "Three candidate next tokens at a step where the model would most likely end its answer, with scores 2, 1 and 0, before and after min_new_tokens blocks the end-of-sequence token (written <end> here). The scores are made up for this figure, not taken from a model; every probability is computed from them as under Calculation."
tags: [inference, generation, length]
updated: 2026-11-23
related: [hyperparameter/max-new-tokens, hyperparameter/num-beams]
sources:
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/logits_process.py", title: "Hugging Face Transformers source: logits_process.py (MinNewTokensLengthLogitsProcessor, MinLengthLogitsProcessor)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py", title: "Hugging Face Transformers source: generation/configuration_utils.py (GenerationConfig: length parameters and defaults)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/utils.py", title: "Hugging Face Transformers source: generation/utils.py (when the filter is added, and the length checks)", trust: 0.9 }
  - { url: "https://huggingface.co/facebook/bart-large-cnn/blob/main/generation_config.json", title: "facebook/bart-large-cnn: generation_config.json", trust: 0.9 }
---

## Definition

**`min_new_tokens`** is the number of tokens a model must write before it is **allowed to
stop**.

- A **token** is the unit of text a language model reads and writes: a word or a piece of a
  word.
- A model ends its answer by writing a special **end-of-sequence token**. It means "the answer
  is finished" and is a candidate at every step, like any other token.
- To write the next token, the model gives every token a **score** (the technical name is
  **logit**), and the scores are turned into **probabilities**: shares that add up to 1.
- While fewer than `min_new_tokens` tokens have been written, the **score of the
  end-of-sequence token is set to minus infinity**. Its probability becomes 0, so it cannot be
  chosen.
- The prompt is **not counted**. The Transformers documentation defines the setting as "the
  minimum numbers of tokens to generate, ignoring the number of tokens in the prompt".

**Analogy:** a meeting with a rule that nobody may leave during the first ten minutes. The rule
does not give anyone more to say; it only keeps the door shut for a while. The analogy shows the
idea only; the exact rule is under Calculation.

## How It Works

**The figure shows one step at which the model would most likely stop**, without and with the
block.

How to read the figure:

- Each bar is one candidate for the next token; its length is the probability that it is
  chosen. `<end>` stands for the end-of-sequence token.
- **First panel (before):** ending is the favourite, at about two chances in three.
- **Second panel (after):** the minimum has not been reached, so `<end>` is removed. It is greyed out
  and has probability 0.
- The two remaining bars are **longer**: the probability that `<end>` held is shared out
  between them, and the model continues with "and" or "but".

What happens step by step (Hugging Face Transformers):

1. Before generating, the library notes the length of the prompt.
2. At each step it counts the **new tokens written so far**.
3. If that count is **below `min_new_tokens`**, the score of every end-of-sequence token is set
   to minus infinity.
4. Otherwise the scores are left alone, and the model may end.
5. The next token is chosen as usual.

What follows from the mechanism:

- **Nothing changes for answers that are long enough anyway.** The block only matters at a step
  where the model would have chosen to end.
- **When it does matter, the model continues with its second choice.** The library's
  documentation shows a model answering "one" without the setting and "one thousand" with
  `min_new_tokens=2`, and comments that the longer output "is not necessarily incorrect".
- **The maximum length still applies.** `max_new_tokens` stops generation regardless of the
  minimum.

Two neighbouring settings:

- **`min_length`** is the older setting. It counts the prompt as well for decoder-only models.
- **`max_new_tokens`** is the opposite bound: the number of new tokens at which generation is
  stopped.

## Calculation

In words: count the new tokens written so far. If the count is below the minimum, give the
end-of-sequence token a score of minus infinity; then turn the scores into probabilities as
usual. A score of minus infinity becomes a probability of 0.

$$
z'_\text{end} =
\begin{cases}
-\infty & \text{if } n < m \\
z_\text{end} & \text{otherwise}
\end{cases}
$$

$$
p_i = \frac{e^{\,z'_i}}{\sum_j e^{\,z'_j}}
$$

| Symbol | Meaning |
| --- | --- |
| $n$ | the number of new tokens written so far (the prompt is not counted) |
| $m$ | the value of `min_new_tokens` |
| $z_\text{end}$ | the model's score for the end-of-sequence token |
| $z'_i$ | the score of token $i$ after the block (unchanged for every other token) |
| $e$ | the mathematical constant 2.718...; $e^{-\infty}$ is 0 |
| $\sum_j$ | "add up over every token $j$" |
| $p_i$ | the probability that token $i$ is chosen |

Steps:

1. **Count** the new tokens so far: total length minus prompt length.
2. **Compare** with `min_new_tokens`.
3. **If below:** replace the end-of-sequence score with minus infinity.
4. **Turn** the scores into probabilities: raise $e$ to each score and divide each result by
   their total.

Which steps are blocked: when the model chooses its **first** new token, 0 have been written;
when it chooses its **k-th**, k - 1 have been written. The block applies while k - 1 is below
the minimum, which is for the first `min_new_tokens` tokens. The end-of-sequence token can
appear at the earliest as token number `min_new_tokens` + 1.

## Worked Example

### Part A: one blocked step

The numbers of the figure: three candidate tokens with scores **`<end>` 2, "and" 1, "but" 0**.
The scores are made up so the arithmetic stays small; a real model scores every token in its
vocabulary at once.

Values of $e^x$ used below, rounded: $e^{2} \approx 7.389$, $e^{1} \approx 2.718$, $e^{0} = 1$.

Without the block:

1. Raise $e$ to each score: 7.389, 2.718, 1.
2. Add up: 7.389 + 2.718 + 1 = 11.107.
3. Divide each by the total:
   - `<end>`: 7.389 / 11.107 ≈ **0.665**
   - and: 2.718 / 11.107 ≈ **0.245**
   - but: 1 / 11.107 ≈ **0.090**

With the block:

1. The score of `<end>` becomes minus infinity, and $e^{-\infty} = 0$.
2. Raise $e$ to each score: 0, 2.718, 1.
3. Add up: 0 + 2.718 + 1 = 3.718.
4. Divide each by the total:
   - `<end>`: 0 / 3.718 = **0**
   - and: 2.718 / 3.718 ≈ **0.731**
   - but: 1 / 3.718 ≈ **0.269**

| Token | Score | Without the block | With the block |
| --- | --- | --- | --- |
| `<end>` | 2 | 0.665 | 0 |
| and | 1 | 0.245 | 0.731 |
| but | 0 | 0.090 | 0.269 |

**What this means:** the model wanted to stop with about two chances in three. With the block
it cannot, and it goes on with "and" (about 73 times in 100) or "but" (about 27 in 100). The
text continues past the point where the model rated it finished.

### Part B: which steps are blocked

Made-up settings: a prompt of **10 tokens**, `min_new_tokens = 5`, `max_new_tokens = 8`.

| New token number | Written before it | Below the minimum of 5? | May the model end here? |
| --- | --- | --- | --- |
| 1 | 0 | yes | no |
| 2 | 1 | yes | no |
| 3 | 2 | yes | no |
| 4 | 3 | yes | no |
| 5 | 4 | yes | no |
| 6 | 5 | no | yes |
| 7 | 6 | no | yes |
| 8 | 7 | no | yes |

1. The first **5** new tokens cannot be the end-of-sequence token.
2. From token **6** on, the model may end.
3. After token **8** generation stops in any case: the total has reached 10 + 8 = 18, the
   maximum.

**What this means:** the output has at least 5 new tokens of text and at most 8 new tokens.
Had the minimum been 9, no step would have allowed the model to end, and the text would be cut
at 8.

## What To Watch

- **Whether it is needed at all.** By default there is no minimum: `min_new_tokens` is not set,
  and `min_length` falls back to 0.
- **Text after the natural end.** When the block is used, read what the model wrote after the
  point where it would have stopped.
- **The gap to the maximum.** Transformers warns of "unfeasible length constraints" when the
  minimum cannot be met inside the maximum length.
- **Which of the two minimums a model ships.** bart-large-cnn ships `min_length=56`, the older
  setting, not `min_new_tokens`.
- **The end-of-sequence token.** The block needs to know which token ends the text. The filter
  is added only when the model's configuration defines one.
