---
id: hyperparameter/do-sample
type: hyperparameter
slug: do-sample
schema_version: 2.2
title: "Do Sample"
name: do_sample
context: inference
priority: 6
importance: important
goals: [inference]
models: [llm]
range: "true | false"
controls: "Whether a language model picks its next word by a random draw that follows the probabilities (true) or always takes the single most likely word (false)."
options:
  - value: "false"
    effect: "Greedy decoding: at every step the model takes the highest-scoring word. Nothing is drawn at random, and temperature, top_k and top_p are not used. This is the fallback of Hugging Face Transformers."
  - value: "true"
    effect: "Sampling: at every step one word is drawn at random, each with the probability the model gives it. Temperature, top_k and top_p shape that draw, and two runs can give different text."
typical_values:
  - setting: "Hugging Face Transformers: used when the model's generation_config.json sets no value"
    value: "false"
    source: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py"
  - setting: "Qwen2.5-7B-Instruct: value in the model's own generation_config.json"
    value: "true"
    source: "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct/blob/main/generation_config.json"
  - setting: "DeepSeek-R1: value in the model's own generation_config.json"
    value: "true"
    source: "https://huggingface.co/deepseek-ai/DeepSeek-R1/blob/main/generation_config.json"
  - setting: "Qwen3-8B: value in the model's own generation_config.json"
    value: "true"
    source: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/generation_config.json"
mistakes:
  - mistake: "Setting temperature, top_p or top_k and leaving do_sample at false"
    why: >-
      The three settings shape a **random draw**, and with `do_sample=False` there is no draw:
      the library takes the highest-scoring token. Transformers adds the temperature, top-k and
      top-p steps only when `do_sample` is true, and logs a warning that the flag "is only used
      in sample-based generation modes".
    fix: >-
      Pass `do_sample=True` together with the sampling settings, or check that the model's
      `generation_config.json` already sets it.
  - mistake: "Passing temperature=0 to get the same answer every time"
    why: >-
      Transformers divides the scores by the temperature and rejects 0 with a `ValueError`. The
      error message itself gives the answer: "If you're looking for greedy decoding strategies,
      set `do_sample=False`."
    fix: >-
      Set `do_sample=False`. That takes the most likely token at every step, which is what a
      temperature of zero would mean.
  - mistake: "Expecting the same text twice with do_sample=True"
    why: >-
      Each token is drawn at random, so two runs can part ways at any step, and every later
      token then builds on a different text.
    fix: >-
      Fix the random seed before generating. The examples in the Transformers source call
      `set_seed(0)` "for reproducibility". For output that must not vary at all, use
      `do_sample=False`.
  - mistake: "Assuming do_sample=True alone samples from the model's full distribution"
    why: >-
      Transformers falls back to `top_k=50` when no value is set, and adds the top-k step
      whenever sampling is on and `top_k` is not 0. Switching sampling on therefore already
      limits the draw to the 50 best-scoring tokens.
    fix: >-
      Pass `top_k=0` to sample with no top-k filter, and read the model's
      `generation_config.json` for the values it ships.
  - mistake: "Asking for several different answers with do_sample=False"
    why: >-
      Greedy decoding has one result per prompt. Transformers raises a `ValueError` when
      `num_return_sequences` is above 1 with `do_sample=False` and no beam search.
    fix: >-
      Set `do_sample=True` to get several different draws, or use beam search (`num_beams`
      above 1) to get the several highest-scoring texts.
visual:
  kind: distribution
  tokens: ["blue", "clear", "falling"]
  logits: [2, 1, 0]
  temperatures: [1]
  caption: "Three candidate next words for \"The sky is\" with scores 2, 1 and 0, and the probability the model gives each (temperature 1, the scores used as they are). With do_sample false the model takes \"blue\" every time; with do_sample true each word is drawn with the probability shown. The scores are made up for this figure, not taken from a model."
tags: [inference, sampling, decoding, greedy]
updated: 2026-11-23
related: [hyperparameter/temperature, hyperparameter/top-p, hyperparameter/top-k, hyperparameter/num-beams]
sources:
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/configuration_utils.py", title: "Hugging Face Transformers source: generation/configuration_utils.py (GenerationConfig: decoding modes, defaults, warnings)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/utils.py", title: "Hugging Face Transformers source: generation/utils.py (the draw, the greedy pick, the sampling-only steps)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/generation/logits_process.py", title: "Hugging Face Transformers source: logits_process.py (TemperatureLogitsWarper)", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/generation_strategies", title: "Hugging Face Transformers documentation: Generation strategies", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/llm_tutorial", title: "Hugging Face Transformers documentation: Text generation", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1904.09751", title: "The Curious Case of Neural Text Degeneration (Holtzman et al., 2019)", trust: 0.9 }
  - { url: "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct/blob/main/generation_config.json", title: "Qwen2.5-7B-Instruct: generation_config.json", trust: 0.9 }
  - { url: "https://huggingface.co/deepseek-ai/DeepSeek-R1/blob/main/generation_config.json", title: "DeepSeek-R1: generation_config.json", trust: 0.9 }
  - { url: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/generation_config.json", title: "Qwen3-8B: generation_config.json", trust: 0.9 }
---

## Definition

**`do_sample`** is a switch with two positions. It decides **how the next token is chosen** from
the model's probabilities.

- A **token** is the unit of text a language model writes: a word or a piece of a word. This
  page says "word" where the difference does not matter.
- To write the next token, the model gives **every** token it knows a **score** (the technical
  name is **logit**), and the scores are turned into **probabilities**: shares that add up to 1.
- **`false`: greedy decoding.** The token with the highest probability is taken, every time.
- **`true`: sampling.** One token is **drawn at random**, and each token's chance of being
  drawn equals its probability. The Transformers documentation calls this multinomial sampling.

**Analogy:** choosing a dish from a menu on which each dish has a popularity share. With the
switch off, you always order the most popular dish. With the switch on, you spin a wheel whose
slices are as large as the shares: the popular dish wins most often, but not always. The analogy
shows the idea only; the exact rule is under Calculation.

## How It Works

**The figure shows the probabilities of three candidate words.** The switch does not change
these probabilities. It changes what is done with them.

How to read the figure:

- Each bar is one candidate word; its length is the probability the model gives it.
- **With `do_sample=False`** the longest bar wins: "blue", every single time. The other two
  words are never written, however often the step is repeated.
- **With `do_sample=True`** the bars are odds. "blue" is drawn about two times in three,
  "clear" about one time in four, "falling" about one time in eleven.
- The panel is labelled "Temperature 1" because the scores are used as they are, with nothing
  reshaping them.

The switch is one of two settings that choose the decoding method in Hugging Face
Transformers. The other is `num_beams`:

| `do_sample` | `num_beams` | Method |
| --- | --- | --- |
| false | 1 | Greedy decoding |
| true | 1 | Multinomial sampling |
| false | above 1 | Beam search |
| true | above 1 | Beam search with sampling |

What the switch turns on and off:

- **Only with `true`** does the library apply the **temperature**, **top-k** and **top-p**
  steps. They reshape or trim the probabilities before the draw.
- **With `false`** those three settings are not used at all.
- The repetition penalty is applied in both positions.

What each position does to the text, as the sources report it:

- The Transformers documentation says greedy decoding "works well for tasks with relatively
  short outputs where creativity is not a priority", but "breaks down when generating longer
  sequences because it begins to repeat itself".
- The same documentation says sampling strategies "reduce repetition and can generate more
  creative and diverse outputs".
- Holtzman et al. measured the other side: sampling from the model's full distribution gave
  incoherent text. This is why sampling is used together with temperature, top-k or top-p.

## Calculation

In words: greedy decoding takes the token with the largest probability. Sampling draws one token
so that, over many draws, each token comes up in proportion to its probability.

$$
\text{greedy: } \; \text{next} = \arg\max_i \, p_i
$$

$$
\text{sampling: } \; P(\text{next} = i) = p_i
$$

| Symbol | Meaning |
| --- | --- |
| $p_i$ | the probability of token $i$ (after temperature, top-k and top-p when sampling) |
| $\arg\max_i$ | "the token $i$ for which the value is largest" |
| $P(\text{next} = i)$ | the chance that token $i$ is the one drawn |

Steps with `do_sample=False`:

1. Compute the scores.
2. Take the token with the highest score. (The highest score always has the highest
   probability, so the library does not need to compute probabilities here.)

Steps with `do_sample=True`:

1. Compute the scores.
2. Apply temperature, top-k and top-p, if set.
3. Turn the scores into probabilities with the **softmax** function: raise the constant $e$
   (about 2.718) to each score and divide each result by their total.
4. Draw one token at random according to those probabilities.

In the Transformers source these are two lines: `torch.multinomial(probs, num_samples=1)` for the
draw, and `torch.argmax(next_token_scores, dim=-1)` for the greedy pick.

## Worked Example

The numbers of the figure: three candidate words for "The sky is" with scores **2, 1 and 0**.
The scores are made up so the arithmetic stays small; a real model scores every token in its
vocabulary at once.

Values of $e^x$ used below, rounded: $e^{2} \approx 7.389$, $e^{1} \approx 2.718$, $e^{0} = 1$.

### The probabilities

1. Raise $e$ to each score: 7.389, 2.718, 1.
2. Add up: 7.389 + 2.718 + 1 = 11.107.
3. Divide each by the total:
   - blue: 7.389 / 11.107 ≈ **0.665**
   - clear: 2.718 / 11.107 ≈ **0.245**
   - falling: 1 / 11.107 ≈ **0.090**

### With do_sample = false

1. The largest probability is 0.665.
2. The model writes **"blue"**.
3. Repeat the step 1,000 times: "blue" 1,000 times.

### With do_sample = true

One way to picture a single draw: lay the three probabilities end to end on a line from 0 to 1,
then pick a random point on the line.

| Word | Its stretch of the line |
| --- | --- |
| blue | 0 to 0.665 |
| clear | 0.665 to 0.910 (0.665 + 0.245) |
| falling | 0.910 to 1 |

1. Random point **0.31**: it falls in the first stretch. The model writes **"blue"**.
2. Random point **0.72**: second stretch. The model writes **"clear"**.
3. Random point **0.95**: third stretch. The model writes **"falling"**.

Repeat the draw 1,000 times and the expected counts are the probabilities times 1,000:

| Word | do_sample = false | do_sample = true (expected) |
| --- | --- | --- |
| blue | 1,000 | about 665 |
| clear | 0 | about 245 |
| falling | 0 | about 90 |

**What this means:** the model's opinion of the three words is the same in both columns. Greedy
decoding hides the second and third choice completely; sampling lets them through as often as
the model rates them.

## What To Watch

- **The value in force.** Transformers uses `do_sample=False` unless the model's
  `generation_config.json` says otherwise. Qwen2.5-7B-Instruct, Qwen3-8B and DeepSeek-R1 all
  ship `true`, so they sample without you asking.
- **Warnings about unused flags.** A log line saying a flag "is only used in sample-based
  generation modes" means a sampling setting was given while `do_sample` is false.
- **Repetition with greedy decoding.** The Transformers documentation names it as the way
  greedy decoding fails on longer outputs.
- **The kind of task.** The Transformers text generation guide says the greedy default "works
  well for input-grounded tasks (transcription, translation)" but is "not optimal for more
  creative use cases (story writing, chat applications)", and that most use cases should set
  the flag to `True`.
- **What else is active.** With sampling on, the fallback `top_k=50` applies unless it is
  changed.
