---
id: hyperparameter/repetition-penalty
type: hyperparameter
slug: repetition-penalty
schema_version: 3.1
title: Repetition Penalty
name: repetition_penalty
context: inference
priority: 4
importance: important
goals:
- inference
models:
- llm
range: 1.0 — 1.2
controls: 'How strongly a language model is discouraged from reusing words that are already in the text: 1.0 changes nothing, higher values make every word that has already appeared less likely to be picked again.'
effects:
  decrease:
  - Words already in the text are held back less
  - At 1.0 there is no penalty at all
  - 'Below 1.0 the effect flips: repeated words become more likely'
  increase:
  - Words already in the text become less likely to be picked again
  - Loops of the same phrase are broken more often
  - Every word that has appeared is hit, including common words and words from the prompt
scale:
  min: 0.8
  max: 1.6
  typical_min: 1
  typical_max: 1.2
  spacing: linear
typical_values:
- setting: 'Hugging Face Transformers: used when the model''s generation_config.json sets no value (no penalty)'
  value: '1.0'
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py
- setting: 'Keskar et al. (2019), CTRL: the value the paper that introduced the penalty reports, used with greedy decoding (written as about 1.2)'
  value: '1.2'
  source: https://arxiv.org/abs/1909.05858
- setting: 'Qwen2.5-7B-Instruct: value in the model''s own generation_config.json'
  value: '1.05'
  source: https://huggingface.co/Qwen/Qwen2.5-7B-Instruct/blob/main/generation_config.json
mistakes:
- mistake: Expecting a word to be penalised more each time it is repeated
  why: 'The penalty does not count. The Transformers documentation states that it "is applied at most once per token": a word that has appeared ten times gets the same reduction as a word that has appeared once.'
  fix: Treat the setting as an on/off mark per word ("has it appeared or not"), with the value setting how large the one reduction is.
- mistake: Forgetting that the prompt counts as text that has already appeared
  why: For decoder-only models (most LLMs), Transformers states that "the considered tokens include the prompt by default". Words you gave the model and want back in the answer (a name, a term, a quoted phrase) are penalised from the first generated token on.
  fix: Lower the value, or build a `RepetitionPenaltyLogitsProcessor` yourself with `prompt_ignore_length` set to the prompt's length and pass it as `logits_processor`, as the example in the library's documentation does.
- mistake: Raising the value well above the published ones to stop every repetition
  why: The penalty hits **every** token that has appeared, not only the looping phrase. Ordinary text needs the same small words and punctuation again and again, and each of them is pushed down too. Keskar et al. report about **1.2** as "a good balance between truthful generation and lack of repetition", and note that the approach works only if the model "has learned a sufficiently reliable distribution".
  fix: Start from the value the model ships with (1.05 for Qwen2.5-7B-Instruct) or from the 1.2 of the CTRL paper, and raise it in small steps while reading the output.
- mistake: Passing a whole number such as 2 instead of 2.0
  why: The filter class of Transformers checks that the penalty is a **float** greater than 0 and raises a `ValueError` ("has to be a strictly positive float") for anything else, including the integer `2`.
  fix: 'Write the value with a decimal point: `repetition_penalty=1.2`.'
visual:
  kind: distribution
  tokens:
  - green
  - blue
  - deep
  - sky
  logits:
  - 2
  - 1
  - 0
  - -1
  penalty:
    value: 1.2
    repeated:
    - blue
    - sky
  caption: Four candidate next words for "The sky is blue and the sea is" with scores 2, 1, 0 and -1, before and after a repetition penalty of 1.2. "blue" and "sky" are already in the text. The scores are made up for this figure, not taken from a model; every probability is computed from them as under Calculation.
tags:
- inference
- sampling
- decoding
- repetition
updated: '2026-11-23'
related:
- hyperparameter/temperature
- hyperparameter/top-p
- hyperparameter/top-k
- hyperparameter/do-sample
sources:
- url: https://arxiv.org/abs/1909.05858
  title: 'CTRL: A Conditional Transformer Language Model for Controllable Generation (Keskar et al., 2019)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/generation/logits_process.py
  title: 'Hugging Face Transformers source: logits_process.py (RepetitionPenaltyLogitsProcessor)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py
  title: 'Hugging Face Transformers source: generation/configuration_utils.py (GenerationConfig: parameters and defaults)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/generation/utils.py
  title: 'Hugging Face Transformers source: generation/utils.py (when the penalty is applied)'
  trust: 0.9
- url: https://huggingface.co/docs/transformers/llm_tutorial
  title: 'Hugging Face Transformers documentation: Text generation (common options)'
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-7B-Instruct/blob/main/generation_config.json
  title: 'Qwen2.5-7B-Instruct: generation_config.json'
  trust: 0.9
---

## Definition

The **repetition penalty** is a number that lowers the score of every token that is **already in
the text**, so that the model is less likely to write it again.

- A **token** is the unit of text a language model writes: a word or a piece of a word. This
  page says "word" where the difference does not matter.
- To write the next token, the model gives **every** token it knows a **score** (the technical
  name is **logit**). A higher score means "fits better here".
- The scores are turned into **probabilities** (shares that add up to 1), and the next token is
  chosen from them.
- The penalty changes the **scores of the tokens that have already appeared**, before they
  become probabilities. All other scores stay as they are.
- A value of **1.0 means no penalty**. Above 1.0 the repeated tokens are pushed down; between 0
  and 1.0 they are pushed up.

Keskar et al. introduced it in the CTRL paper as "penalized sampling", which "works by
discounting the scores of previously generated tokens".

**Analogy:** a speaker who notes every word already said and reaches for it a little less
readily the next time. The note is a tick mark, not a tally: a word said five times is held back
no more than a word said once. The analogy shows the idea only; the exact rule is under
Calculation.

## How It Works

**The figure shows one set of scores turned into probabilities twice**: as the model produced
them, and after a penalty of 1.2.

How to read the figure:

- Each bar is one candidate word; its length is the probability that this word is picked.
- The text so far is "The sky is blue and the sea is". Two of the four candidates, **"blue"**
  and **"sky"**, are already in it.
- **First panel (before):** the probabilities from the model's own scores.
- **Second panel (after):** the bars of "blue" and "sky" are **shorter**. The line under the panel shows
  how their scores changed.
- The bars of "green" and "deep" are **longer**, although their scores were not touched: the
  probability the repeated words lost is shared out among the others.

What happens step by step when a model writes one token (Hugging Face Transformers):

1. The model computes a score for every token.
2. The library looks up **which tokens are already in the text**, prompt included.
3. The score of each such token is **lowered once**, by the rule under Calculation.
4. Temperature, top-k and top-p follow, if sampling is on.
5. The scores are turned into probabilities, and the next token is chosen.

Three properties of the Transformers implementation:

- **It does not need sampling.** The penalty is applied with greedy decoding and with beam
  search as well as with `do_sample=True`. It is skipped only when the value is exactly 1.0.
- **It is applied once per token**, however often the token has appeared.
- **The prompt counts.** For decoder-only models, the tokens of the prompt are treated as
  already used.

Why a penalty at all:

- Keskar et al. start from a dilemma. Choosing the most likely token every time gives the
  model's best guess, but "is known to create repetitions of phrases or sentences even for large
  well-trained models".
- Their answer is to stay close to the most likely token and to prevent "repetitions through a
  penalty".

## Calculation

In words: for each token that is already in the text, make its score smaller. A positive score is
**divided** by the penalty; a negative score is **multiplied** by it. Both moves lower the score
when the penalty is above 1.

$$
z'_i =
\begin{cases}
z_i / \theta & \text{in the text, } z_i \ge 0 \\
z_i \times \theta & \text{in the text, } z_i < 0 \\
z_i & \text{not in the text}
\end{cases}
$$

| Symbol | Meaning |
| --- | --- |
| $z_i$ | the score (logit) of token $i$ from the model |
| $\theta$ | the repetition penalty, a number greater than 0; 1.0 means no change |
| $z'_i$ | the score of token $i$ after the penalty |

Steps:

1. **List** the tokens that are already in the text.
2. For each of them, **look at the sign** of its score.
3. **Positive or zero:** divide the score by the penalty.
4. **Negative:** multiply the score by the penalty.
5. **Turn** all the scores into probabilities as usual: raise the constant $e$ (about 2.718) to
   each score and divide each result by their total (the **softmax** function).

Where the two cases come from:

- This is the rule of the `RepetitionPenaltyLogitsProcessor` in Hugging Face Transformers. Its
  source carries the comment: "if score < 0 then repetition penalty has to be multiplied to
  reduce the token probabilities".
- The **CTRL paper** writes one case only: the score of a generated token is divided by the
  penalty (inside the temperature formula, as $x_i / (T \cdot \theta)$).
- Dividing a **negative** score by a number above 1 moves it toward zero, which **raises** it.
  The library's second case avoids that.

Limit of the value in Transformers: the penalty must be a float greater than 0; anything else
raises a `ValueError`.

## Worked Example

The numbers of the figure: the text so far is "The sky is blue and the sea is", and four
candidate next words have the scores **green 2, blue 1, deep 0, sky -1**. The penalty is
**1.2**. The scores are made up so the arithmetic stays small; a real model scores every token
in its vocabulary at once.

Values of $e^x$ used below, rounded: $e^{2} \approx 7.389$, $e^{1} \approx 2.718$,
$e^{0.833} \approx 2.301$, $e^{0} = 1$, $e^{-1} \approx 0.368$, $e^{-1.2} \approx 0.301$.

### Before the penalty

1. Raise $e$ to each score: 7.389, 2.718, 1, 0.368.
2. Add up: 7.389 + 2.718 + 1 + 0.368 = 11.475.
3. Divide each by the total:
   - green: 7.389 / 11.475 ≈ **0.644**
   - blue: 2.718 / 11.475 ≈ **0.237**
   - deep: 1 / 11.475 ≈ **0.087**
   - sky: 0.368 / 11.475 ≈ **0.032**

### The penalty

1. **Already in the text:** "blue" and "sky". "green" and "deep" are not.
2. **blue** has a positive score, so divide: 1 / 1.2 ≈ **0.833**.
3. **sky** has a negative score, so multiply: -1 × 1.2 = **-1.2**.
4. **green** stays at 2 and **deep** stays at 0.

### After the penalty

1. Raise $e$ to each new score: 7.389, 2.301, 1, 0.301.
2. Add up: 7.389 + 2.301 + 1 + 0.301 = 10.991.
3. Divide each by the total:
   - green: 7.389 / 10.991 ≈ **0.672**
   - blue: 2.301 / 10.991 ≈ **0.209**
   - deep: 1 / 10.991 ≈ **0.091**
   - sky: 0.301 / 10.991 ≈ **0.027**

### Side by side

"blue" and "sky" are the two words already in the text.

| Word | Score | Score after | Probability | Probability after |
| --- | --- | --- | --- | --- |
| green | 2 | 2 | 0.644 | 0.672 |
| blue | 1 | 0.833 | 0.237 | 0.209 |
| deep | 0 | 0 | 0.087 | 0.091 |
| sky | -1 | -1.2 | 0.032 | 0.027 |

**What this means:** out of 1,000 picks by sampling, "blue" would be chosen about 209 times
instead of 237, and "sky" about 27 times instead of 32. The model can still repeat a word; it is
only less likely to.

### Why the negative score is multiplied

Take "sky" with its score of -1 and divide it by 1.2, as the formula of the CTRL paper would:

1. -1 / 1.2 ≈ **-0.833**.
2. -0.833 is **higher** than -1, so the word would become more likely, the opposite of a
   penalty.
3. Multiplying gives -1.2, which is lower than -1. This is what Transformers does.

## What To Watch

- **Whether the output repeats.** The Transformers documentation advises setting the value
  above 1.0 "if you're seeing the model repeat itself often", and adds that larger values apply
  a larger penalty.
- **The value in force.** Transformers reads `repetition_penalty` from the model's
  `generation_config.json` and uses 1.0 (no penalty) when that file sets none.
  Qwen2.5-7B-Instruct ships 1.05.
- **Words from the prompt.** They are penalised by default. Watch for answers that avoid a
  name or term the prompt contains.
- **Text that must reuse tokens.** The penalty lowers every token that has appeared, so judge
  the output on text where the same tokens have to come back.
- **Values below 1.0.** They reward repetition instead of discouraging it.
