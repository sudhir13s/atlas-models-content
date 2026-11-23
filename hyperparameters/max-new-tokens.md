---
id: hyperparameter/max-new-tokens
type: hyperparameter
slug: max-new-tokens
schema_version: 3.1
title: Max New Tokens
name: max_new_tokens
context: inference
priority: 3
importance: critical
goals:
- inference
models:
- llm
range: 20 — 512
controls: 'The largest number of tokens (words or pieces of words) a language model may write in one answer, not counting the prompt: when the count is reached, generation stops, finished or not.'
effects:
  decrease:
  - The answer is cut off sooner
  - Too low, and the text stops in the middle of a sentence
  - The longest possible wait gets shorter
  increase:
  - More room before the answer is cut off
  - A model that finishes on its own is not made to write more
  - The longest possible wait gets longer
scale:
  min: 1
  max: 100000
  typical_min: 20
  typical_max: 512
  spacing: log
typical_values:
- setting: 'Hugging Face Transformers: what a generate call produces at most when neither max_new_tokens nor max_length is set'
  value: '20'
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/generation/utils.py
- setting: 'Hugging Face Transformers documentation: the value in the sampling and beam search examples of the generation guide'
  value: '50'
  source: https://huggingface.co/docs/transformers/generation_strategies
- setting: 'Qwen2.5-7B-Instruct: the value in the quickstart code of the model card'
  value: '512'
  source: https://huggingface.co/Qwen/Qwen2.5-7B-Instruct
- setting: 'Original Transformer (Vaswani et al., 2017), machine translation: maximum output length, set relative to the input'
  value: input length + 50
  source: https://arxiv.org/abs/1706.03762
mistakes:
- mistake: Not setting it at all
  why: Without a value, Hugging Face Transformers falls back to a total length setting of 20 and generates **at most 20 new tokens**, unless the model's own configuration says otherwise. Most answers are longer than that and come back cut off. The library logs a warning and recommends setting `max_new_tokens`.
  fix: 'Always pass `max_new_tokens`. The Transformers documentation says: "Be sure to define it, as it usually defaults to a small value."'
- mistake: Confusing it with max_length
  why: '`max_length` is the older setting and counts the **whole sequence**, which for a decoder-only model includes the prompt. With a prompt of 30 tokens, `max_length=40` leaves 10 new tokens, and a prompt as long as `max_length` raises a `ValueError`. When both are set, `max_new_tokens` takes precedence and the library logs a warning.'
  fix: Use `max_new_tokens` only. The Transformers documentation recommends it and keeps `max_length` "for backward compatibility".
- mistake: Raising it to make answers longer
  why: The setting is an **upper limit**, not a target. A model stops on its own when it writes its end-of-sequence token, and a higher limit does not delay that.
  fix: To require a minimum length, use `min_new_tokens`. To get a longer answer, change the prompt.
- mistake: Counting in words
  why: The limit counts **tokens**, and a token is a word or a piece of a word. The same number of tokens is a different number of words for different texts and different models.
  fix: Count the tokens of a typical answer with the model's own tokenizer, and set the limit with room to spare.
- mistake: Assuming a hosted API counts the same things
  why: Services define their own limit. The OpenAI Chat Completions API calls it `max_completion_tokens` and describes it as an upper bound that includes "visible output tokens and reasoning tokens", so the visible answer can be shorter than the number set.
  fix: Read the API reference of the service for what its limit counts.
tags:
- inference
- generation
- length
updated: '2026-11-23'
related:
- hyperparameter/min-new-tokens
- hyperparameter/num-beams
sources:
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py
  title: 'Hugging Face Transformers source: generation/configuration_utils.py (GenerationConfig: length parameters, defaults, checks)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/generation/utils.py
  title: 'Hugging Face Transformers source: generation/utils.py (how the length limits are prepared and checked)'
  trust: 0.9
- url: https://huggingface.co/docs/transformers/generation_strategies
  title: 'Hugging Face Transformers documentation: Generation strategies'
  trust: 0.9
- url: https://huggingface.co/docs/transformers/llm_tutorial
  title: 'Hugging Face Transformers documentation: Text generation'
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-7B-Instruct
  title: Qwen2.5-7B-Instruct model card
  trust: 0.9
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.9
- url: https://developers.openai.com/api/reference/resources/chat
  title: 'OpenAI API reference: Chat Completions'
  trust: 0.85
---

## Definition

**`max_new_tokens`** is the largest number of tokens a model may **add** to the prompt in one
generation.

- A **token** is the unit of text a language model reads and writes: a word or a piece of a
  word.
- The **prompt** is the text you give the model. It is counted in tokens too, but **not**
  against this limit.
- A model writes one token at a time. It stops in one of two ways:
  - **On its own:** it writes a special **end-of-sequence token**, which means "the answer is
    finished".
  - **By the limit:** the number of new tokens reaches `max_new_tokens`. The text ends there,
    even in the middle of a sentence.
- The Transformers documentation defines it as "the maximum numbers of tokens to generate,
  ignoring the number of tokens in the prompt".

**Analogy:** the length limit of an answer box on a form. A short answer fits and simply ends;
a long answer is cut where the box ends. A bigger box does not make anyone write more. The
analogy shows the idea only; the exact rule is under Calculation.

## How It Works

**The setting is a stop condition, checked after every token.**

What happens step by step (Hugging Face Transformers):

1. Before generating, the library counts the tokens of the prompt.
2. It adds `max_new_tokens` to that count. The sum is the **total length** at which generation
   must stop.
3. The model writes one token.
4. If that token is the end-of-sequence token, generation stops: the answer is **complete**.
5. If the total length has been reached, generation stops: the answer is **cut off**.
6. Otherwise the model writes the next token, from step 3.

What the limit does and does not do:

| | |
| --- | --- |
| It **does** | bound how long one generation can run |
| It **does** | cut the text when the model has not finished by then |
| It **does not** | make a model that finishes early write more |
| It **does not** | count the prompt |

What happens when you leave it out:

- The library has no value for `max_new_tokens` of its own. It falls back to the older
  `max_length` setting, whose fallback value is 20, and treats that as **20 new tokens**.
- The Transformers documentation states the result: generation "returns up to 20 tokens by
  default unless otherwise specified" in the model's configuration.
- A model can ship its own limit in its `generation_config.json`, and that value is then used
  instead.

Two neighbouring settings:

- **`max_length`** counts the prompt and the new tokens together. It remains "for backward
  compatibility".
- **`min_new_tokens`** is the opposite bound: a number of new tokens that must be written
  before the model is allowed to stop on its own.

## Calculation

In words: the total length at which generation stops is the length of the prompt plus
`max_new_tokens`. The number of new tokens actually written is the smaller of two numbers: the
limit, and the number the model needs before it writes its end-of-sequence token.

$$
L_\text{stop} = L_\text{prompt} + M
$$

$$
N_\text{written} = \min(M,\; N_\text{model})
$$

| Symbol | Meaning |
| --- | --- |
| $L_\text{prompt}$ | the number of tokens in the prompt |
| $M$ | the value of `max_new_tokens`; it must be greater than 0 |
| $L_\text{stop}$ | the total length (prompt plus new tokens) at which generation is stopped |
| $N_\text{model}$ | the number of tokens the model would write before ending on its own, end-of-sequence token included |
| $N_\text{written}$ | the number of new tokens actually written |
| $\min$ | "the smaller of the two" |

Steps:

1. **Count** the tokens of the prompt.
2. **Add** `max_new_tokens`. This is the stopping length. Transformers stores it as
   `max_length = max_new_tokens + prompt length`.
3. **Compare** with what the model needs: if the model ends on its own first, the answer is
   complete; otherwise it is cut at the limit.

Limits of the value in Transformers:

- `max_new_tokens` of 0 or less raises a `ValueError`.
- If `max_new_tokens` and `max_length` are both set, `max_new_tokens` wins.

## Worked Example

Made-up numbers, small enough to follow: a prompt of **12 tokens**, and a model that would
finish its answer after **31 new tokens** (30 tokens of text and the end-of-sequence token).

### With max_new_tokens = 50

1. Stopping length: 12 + 50 = **62** tokens.
2. The model ends on its own after 31 new tokens, at a total of 12 + 31 = 43.
3. 43 is below 62, so the limit is never reached.
4. New tokens written: min(50, 31) = **31**. The answer is **complete**.

### With max_new_tokens = 20

1. Stopping length: 12 + 20 = **32** tokens.
2. After 20 new tokens the total is 32. The model has not finished: it needed 31.
3. Generation stops at the limit.
4. New tokens written: min(20, 31) = **20**. The answer is **cut off** 11 tokens early.

### With nothing set

1. The library falls back to 20 new tokens.
2. The result is the same as with `max_new_tokens = 20`: **20** new tokens, cut off.

### With max_new_tokens = 500

1. Stopping length: 12 + 500 = **512** tokens.
2. The model still ends on its own after 31 new tokens.
3. New tokens written: min(500, 31) = **31**. The same complete answer as with 50.

| max_new_tokens | Stopping length | New tokens written | Answer |
| --- | --- | --- | --- |
| not set | 12 + 20 = 32 | 20 | cut off |
| 20 | 32 | 20 | cut off |
| 50 | 62 | 31 | complete |
| 500 | 512 | 31 | complete |

**What this means:** below what the answer needs, the limit decides where the text ends. Above
it, the limit changes nothing: 50 and 500 give the same answer.

## What To Watch

- **Whether the answer was cut.** A text that stops mid-sentence, or an output exactly as long
  as the limit, points to the limit and not to the model.
- **The warning about the default.** Transformers warns when it is "using the model-agnostic
  default `max_length`" and recommends setting `max_new_tokens`.
- **The value the model ships with.** A `generation_config.json` can carry its own length
  limit, which applies when you pass none.
- **The other bound.** If `min_new_tokens` plus the prompt is larger than the stopping length,
  Transformers warns that the constraints are unfeasible, and generation stops at the maximum.
- **What a service counts.** The OpenAI API's `max_completion_tokens` includes reasoning tokens
  as well as visible output.
