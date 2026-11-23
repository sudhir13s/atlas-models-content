# CONVENTIONS.md — authoring rules

The rules every file follows. Humans and agents follow the identical process. The authoritative
cross-repo spec is the
[content-repo standard](https://github.com/sudhir13s/TheAIAtlas/blob/main/docs/content-repo-standard.md);
this file is the `models`-domain instance.

## Slugs and file names

- Slugs are **lowercase, hyphen-separated, ASCII** — no spaces, no underscores. Example:
  `learning-rate`, `llama-3-1-8b-instruct`, `mixture-of-experts`.
- The file name is `<slug>.md` and **must equal** the `slug` frontmatter field.
- A file lives in the folder mapped to its `type` in `schema/registry.json`.

## IDs

- `id` = `<type>/<slug>` (e.g. `hyperparameter/learning-rate`). Globally unique, **immutable**.
- All cross-references use IDs, never titles: `related`, `led_to`, `influenced_by`,
  `models_using`, `architecture_id`, `license` (when it points at a `license` file), etc.
- Every referenced ID must resolve to an existing file. Dangling references fail validation.

## Frontmatter

Optional on every file: `locked` — field paths no job may change.

Base fields on **every** file: `id`, `type`, `slug`, `schema_version`, `title`, `tags`, `updated`,
`sources`. Optional base field: `related`. Each type adds its own fields — see
`schema/<type>.schema.json`. Put **structured/reference data in frontmatter** (arrays, matrices,
numeric config) so it stays machine-parseable.

- `tags`: lowercase, hyphen-separated.
- `updated`: ISO date (`YYYY-MM-DD`).
- `sources[]`: `{ url, title, trust }` — `trust` in `[0,1]`. Open-source-first; cite, do not copy.
- `schema_version`: the `major.minor` the file was authored against (the repo is at `3.0`). A
  type whose schema fixes the value (`model`: `3.0`) is refused with any other, so every record of
  that type carries the current version; a breaking change raises it and converts every record.

## Body sections

- Prose lives under `##` headings. The **required sections** and their order per type are declared
  in `schema/registry.json → types.<type>.sections`; `optional_sections` may be absent and, when
  present, follow the required ones in that order.
- Formulas may use LaTeX (`$...$` / `$$...$$`) — consumers render with KaTeX/MathJax.
- Code examples use fenced blocks with a language tag (```python).
- The `Example` section is **mandatory** wherever the type declares it — a worked, concrete example
  is what makes reference content usable. Never leave it empty or hand-wavy.

## Per-type specifics

- **hyperparameter** — carries `context` (`training` | `inference`) and a `priority` integer
  (1 = most impactful; ordering is per `context`) plus `importance` (`critical`/`important`/`optional`).
  A record is written in one of two forms; the pages of both are valid until every record is converted:
  - **Schema 2.2 (write new and revised records this way).** The page is **visual-first**, in this
    order: `controls` (one plain sentence under the title) → the effect scale drawn from `effects`
    and `scale` → Definition → How It Works with the figure chosen by `visual` → `typical_values` →
    Calculation → Worked Example → `mistakes` → What To Watch → `related` and `sources`.
    Body sections: Definition → How It Works → Calculation → Worked Example → What To Watch.
  - **Schema 1.0 (the records not yet converted).** `starting_value`, `if_increased`,
    `if_decreased`, `common_mistake`; body sections Definition → What to look for → Calculation →
    Example.
  - **The effect scale.** A numeric setting gives `scale` (`min`, `max`, the typical band,
    `spacing`); the consumer draws the axis in three zones (below typical, the band, above typical)
    with tick values, and marks on it every `typical_values` row whose `value` is one plain number
    inside the scale (`0.9`, `50`, `1e-3`). Write such a value as the bare number and put the
    context in `setting`; a range or several values in one row stays in the table only. A setting
    with no numeric scale (a switch, a named choice) gives `options` (`value`, `effect`) instead of
    `scale`, and may leave `effects` out; the consumer draws one tile per option.
  - **Figures are computed, never drawn by hand.** `visual.kind` is `schedule` (the learning rate
    at every step), `distribution` (next-token probabilities of a few example scores) or `beams`
    (two steps of beam search over a small table of probabilities); the consumer computes every
    point from the values in the record. A `distribution` sets exactly one of `temperatures` (one
    panel per temperature), `truncate` (`top_k`, `top_p` or `remove`: before and after candidates
    are removed) and `penalty` (`value`, `repeated`: before and after the repetition penalty), each
    computed as Hugging Face Transformers does. The optional `panel_titles` replace the default
    panel titles, for scores that are not next-token scores. A value that is an example and not a
    published one is named as such in `caption`.
  - **Write for a newcomer, in depth.** Explain each term in plain words at first use, spell out
    acronyms, say in words what a formula computes before showing it, and mark an analogy as an
    analogy. A section is as long as understanding needs; break it into short paragraphs, bullets,
    numbered steps and small tables, with the load-bearing words in bold.
  - **Calculation and Worked Example can be redone by hand.** Every symbol defined, every step
    numbered with the numbers substituted and the result, small numbers preferred, and a closing
    line "What this means". The worked example's numbers appear on the figure (`visual.marks`, or
    the same inputs).
  - **Every value has its source.** Each `typical_values` row names who used the value and links
    one entry of `sources`; recommendations are attributed to who makes them; `range` and the
    `scale` band span the `typical_values`. `related` lists the settings this one interacts with.
- **parameter-calculator** — one file per architecture family (`arch`), with a ranked `components[]`
  list and a `worked_example.config` the consumer live-computes. Body explains each component + the
  master formula.
- **parameter** — one structural setting of a model's configuration: `name` (the exact config key),
  `applies_to` (architecture families), `priority`, `importance`. A record is written in one of two
  forms:
  - **Schema 2.9 (the teaching form; write new and revised records this way).** It takes the
    hyperparameter 2.2 fields by the same names and shapes (`controls`, `effects`, `scale`,
    `typical_values`, `mistakes`, `related`), read by the same consumer mapping. The page is
    visual-first: `controls` under the title → the figure chosen by `visual` with the Definition
    under it → How It Works → `typical_values` (drawn as "Values in real models") → Calculation
    (how the key enters the parameter count, linking the parameter calculators) → Worked Example →
    the effect scale from `effects` and `scale` → `mistakes` (drawn as common misunderstandings) →
    What To Watch → `related` and `sources`. `if_increased`, `if_decreased` and `formula` are left
    out.
  - **Schema 1.0 (the records not yet converted).** `if_increased`, `if_decreased`, `formula`;
    body sections Definition → What to look for → Calculation → Example.
  - **Every value names its model.** Each `typical_values` row says which model the value comes
    from and links that model's `config.json`, card or paper; a value the configuration and the
    card state differently (a context length) is given with both and explained.
  - **`visual`** is computed from a real model's configuration copied into `model` (or `models`):
    `vocab_size`, `hidden_size`, `intermediate_size`, `num_hidden_layers`, `num_attention_heads`,
    `num_key_value_heads`, `head_dim`, `tie_word_embeddings`, plus `ffn_matrices` (3 gated, 2
    plain) and `qk_norm`, each read from the model's official implementation. The count follows
    the implementation matrix by matrix, so the figure reproduces the model's published total.
    - `count_sweep`: the count per layer or in total as `vary` takes each of `values`.
    - `count_breakdown`: each part's share of the total (embedding, attention, feed-forward,
      norms, output head).
    - `head_grid`: the query heads as a grid, each with the key-value head it reads.
    - `kv_cache`: keys and values cached per token and for `tokens` tokens, against one key-value
      head per query head.
    - `context_cost`: attention-score entries in one layer and the KV cache against context
      length, `marks` naming lengths such as the native context and `max_position_embeddings`.
  - **Convolutional and recurrent `visual` kinds** (`schema/_parameter-cnn-rnn-visuals.schema.json`)
    take a published network's layer shapes, or small grids named as an example in `caption`, and
    compute as PyTorch's Conv2d, MaxPool2d, AvgPool2d, BatchNorm2d, RNN, GRU and LSTM do.
    - `conv_geometry`: the padded input with one window's taps shaded and its span outlined, and
      the output, for each panel's stride, padding and dilation.
    - `conv_params`: kernel × kernel × in × out weights plus out biases per layer, summed per
      `part`; a fully connected layer is written as the convolution it equals.
    - `pooling`: max or average windows of `size` at `stride` on one grid.
    - `batch_norm`: one channel's values normalised with their mean and variance (divided by n),
      then γ × x̂ + β.
    - `recurrent_sweep`, `recurrent_layers`, `gate_blocks`: blocks × hidden × (input + hidden) plus
      `biases` bias vectors per block (0 weights only, 1, or 2 as PyTorch), against hidden size,
      per layer of a stack, or per block. Every count states its bias convention.
  - **Transformer-function and mixture-of-experts `visual` kinds**
    (`schema/_parameter-transformer-visuals.schema.json`) compute as Hugging Face Transformers does;
    example inputs are named as such in `caption`.
    - `activation_curves`: relu, gelu (exact), gelu_new (tanh approximation) and silu over `range`,
      the values at `marks` tabulated.
    - `norm_compare`: one `vector` through LayerNorm and RMSNorm (gain 1, bias 0) with a model's
      `eps`.
    - `rope_rotation`: an example query and key turned by position × rope_theta^(−2·pair ÷ head_dim).
    - `moe_sweep`, `moe_count`: total and active parameters of a decoder whose every layer is a
      mixture of experts (`decoder_config` plus `num_experts`, `num_experts_per_tok`,
      `moe_intermediate_size`), against one expert value or part by part.
    - `expert_layer`: one layer's routed experts, shared experts and router, stored and per token.
  - **`options`** replaces `scale` for a switch or a named choice (bidirectional, the cell type),
    with the hyperparameter shape and drawing.
  - **The Worked Example uses a catalog model's own numbers**, names the model, and can be redone
    by hand with every step numbered; with no catalog model, a published network's layers.
- **architecture-era** — carries `led_to[]` / `influenced_by[]` (IDs) forming the evolution DAG,
  plus `era`, `year`, `family`. Body sections: What Changed → Key Ideas, then the optional
  How It Works → Worked Example. A record is written in one of two forms:
  - **Schema 2.5 (the teaching form; write new and revised records this way).** The page is
    visual-first: `description` is one plain sentence saying what the architecture is → the main
    figure → How It Works (numbered steps tied to the figure's blocks: what goes in, what each
    block does, what comes out) → Worked Example (the figure `visual` computes, then the core
    computation on tiny numbers, every step numbered and recomputed, closing with "What this
    means") → `key_equation` → `comparison` (a table of what changed from the approach before,
    with the What Changed section explaining it) → `key_numbers` (the configuration the original
    paper or named models publish) → `misunderstandings` (two to four, each corrected from a
    source) → analogy, strengths and limitations, story → `notable_models` and `sources`.
    `build_steps` and `eli5` are left out: How It Works replaces both.
  - **Earlier records** keep `build_steps` and `eli5`; their pages are unchanged until converted.
  - **`visual`** is the Worked Example's computed figure. `kind` names the computation and the
    other fields are its inputs, exactly the numbers the Worked Example uses; the consumer
    computes everything it draws from them, so the figure cannot disagree with the text. The
    caption says what is drawn and, where the numbers are made up, that they are an example.
    - `distribution` (see hyperparameter): softmax bars of example scores (attention scores,
      router scores), with `panel_titles` naming each panel.
    - `recurrent_memory`: a plain network hₜ = tanh(w·hₜ₋₁ + xₜ) beside one LSTM cell from its
      gate sums, and the share of an error left after n steps back.
    - `convolution`: an image, a filter, the feature map, ReLU and max-pooling, with the window
      worked by hand outlined.
    - `noise_schedule`: √ᾱ_t and √(1 − ᾱ_t) over DDPM's linear β schedule, `marks` tabulated.
    - `residual_gradient`: the gradient through n blocks, wⁿ (plain) against (1 + w)ⁿ (residual).
    - `discriminator`: p_data, p_g and D* = p_data ÷ (p_data + p_g) per item, and the game's value.
    - `rotary`: a query and a key turned by position × angle, and their score, per pair.
    - `selective_scan`: a one-number state space model by zero-order hold, each token's own Δ
      against one fixed Δ.
    - `loss_formula`: L(N, D) = E + A ÷ N^α + B ÷ D^β with the cited constants, against N at each
      model's token count.
    - `group_advantages`: the rewards of one group and their group-relative advantages.
    - `perceptron_training`: the perceptron rule from zero weights, one panel per correction.
    - `backprop`: one forward and backward pass through a 2-2-1 sigmoid network and one update.
    - `gaussian_code`: each code dimension's N(μ, σ²) against N(0, 1), z = μ + σ·ε, and the KL
      divergence.
  - **Every row has its source.** Each `comparison` row, `key_numbers` row and misunderstanding
    names one entry of `sources`, fetched for the record; findings are attributed to who reports
    them, and no number is written from memory.
- **model** — one record per base model (schema 3.0). Carries the `fingerprint`, `organization` and `license`
  (IDs; `license_note` names a licence with no record), `variants[]` (quantised or repackaged copies:
  name, format, address, publisher), `aliases[]`, `hub_signals`, `gguf_repositories` and lineage (benchmark values live in `score-sheet` records of
  `atlas-evaluation-content`).
  - **Every fact is stored once.** `context_window_tokens` and `parameters_active_b` are top-level
    fields only; `attention_type` is in `fingerprint` only; the release year and month are read
    from `release_date` (`YYYY-MM-DD`). A consumer derives the rest.
  - **Mixture of experts has one stored statement, `filters.is_moe`:** true for a mixture of
    experts, false when the configuration was read and names no routed experts, absent when no
    source says. `moe_config` holds the routing facts and exists only with `is_moe: true` (never
    null). The `moe` and `dense` tags are derived, not stored, and the consumer's rule that sets
    `architecture_id` reads the flag, so `architecture-era/mixture-of-experts` follows it.
  - **`tags` hold only what no field states** (the model type, such as `bert` or `vit`): never the
    pipeline tag, the attention type, `llm`, `moe` or `dense`. A consumer that shows tags adds
    those from the fields.
  - **Prose lives in the frontmatter only.** A record without `summary` has none yet. The prose
    queue writes up to five passages (`summary`, `intended_use`, `what_sets_it_apart`,
    `running_it`, `limitations`), with the span of the source behind each in `quotes`. The body
    never repeats a passage: most records have no body. A hand-written record may keep its own
    long-form sections (Summary → Why It Matters → Notes), which no job writes.
  - **`model_category` says what the model is for; `model_kind` says how it is built**, and
    decides which architecture fields apply:

    | `model_kind` | What it is | Architecture blocks |
    |---|---|---|
    | `transformer-language` | one transformer stack over text tokens | `fingerprint` required unless the repository is gated; `pipeline` optional |
    | `transformer-vision` | a transformer over image or video patches | `fingerprint`, `pipeline` or both |
    | `multimodal` | encoders of more than one kind of input feeding one model | `pipeline`; `fingerprint` for its language stack |
    | `convolutional` | a convolutional network | `pipeline` only: no `fingerprint`, `moe_config`, `context_window_tokens` or `filters.is_moe` |
    | `diffusion` | a network called in a denoising loop | `pipeline`; `fingerprint` when it also has a transformer stack |
    | `audio-speech` | a model that reads or writes sound | `fingerprint`, `pipeline` or both |
    | `embedding` | a model that returns one vector per input | either block; the only kind with `embedding_dimension` |
    | `tabular-time-series` | a network over table cells or time-series patches | `pipeline`; `fingerprint` when it states a stack |
    | `control-policy` | a network that outputs a robot's actions | `pipeline` |

    A job gives the kind by a fixed rule over the record's own fields; a record the rule cannot
    place has no `model_kind` until a person reads it and sets one. It is never guessed. A gated
    record whose configuration cannot be read may have neither block.
  - **A sparse field is allowed only where it applies:** `fingerprint.input_stem` on an
    `audio-speech` record, `fingerprint.decoder_layers` and `decoder_positional_encoding` with
    `stack: encoder-decoder`, `moe_config` with `filters.is_moe: true`.
  - **Closed value sets are enums:** `fingerprint.attention_type` (`MHA`, `MQA`, `GQA`, `MLA`),
    `normalization` (`RMSNorm`, `LayerNorm`), `positional_encoding` (`RoPE`, `Learned`, `ALiBi`,
    `Sinusoidal`, `Convolutional`). `activation_function` keeps the configuration's name in one
    spelling per function: `silu` (never `swish`), `gelu_new` (never `gelu_pytorch_tanh`).
  - **No unknown fields,** at the top level or inside `fingerprint`, `moe_config`, `filters`,
    `hub_signals`, `lineage` and `pipeline`.

  A field with no source is absent, never `0` or `Unknown`; a passage the source cannot support is
  absent. `fingerprint.norm_placement` (`pre` | `post` | `both`) and `fingerprint.stack`
  (`encoder` | `decoder` | `encoder-decoder`) are read from the model's official implementation or
  paper, which is then listed in `sources`; a block with any other layout leaves both absent, and a
  consumer draws no block figure without them. The same source gives `fingerprint.decoder_layers`
  (an encoder-decoder's decoder depth; `num_layers` is then the encoder's, and the other block
  fields must hold for both stacks), `fingerprint.embedding_norm` (a norm on the summed embeddings),
  `fingerprint.input_stem` (how an audio model turns its signal into a sequence),
  `fingerprint.decoder_positional_encoding`, `fingerprint.parallel_sublayers` (attention and
  feed-forward side by side on the block's input: `shared-norm` or `separate-norms`) and
  `fingerprint.output_head` (a table model's cell selection head, with or without aggregation).
  `fingerprint.layer_mixers` is set when the layers do not all hold the same mixer: the mixers'
  names as the code gives them, the pattern counted from the configuration's per-layer list, and
  whether every layer also has its own feed-forward.
  A gated model's numbers may come from its maker's paper instead of its configuration; a value
  the paper and a sibling's configuration define differently (vocabulary size with special
  tokens) is then left out. `pipeline` is the whole model as ordered `stages`
  (`id`, `role`, `title`, optional `details[]`, `note`, `frame` with `repeat`, `inner_frame` with
  `inner_repeat` for a repeated block inside a loop, `side` with `into` for each further side
  input, `branch` with `branch_from` for a second output), for models
  that are more than one transformer stack: every stage and its place come from one primary source
  (the paper's architecture figure or text, the card, the configuration or the official code),
  named in `pipeline.source` and listed in `sources`. The figure is drawn from these words in the
  site's own style, never copied or traced from the source's image; a stage the source does not
  state is left out, and with no such source the record has no `pipeline`. `embedding_dimension` is the vector length an
  embedding model returns, copied from its card or pooling configuration (never from
  `fingerprint.hidden_size`). `base_models[]` (`name`, `address`) names the bases that have no
  record here, nearest first; a base with a record goes in `lineage.parents`. An arXiv entry of
  `sources` carries the paper's title as the arXiv API gives it, copied by code, not the bare ID.
  `gguf_repositories` is written by `models-scout` only: links (`publisher`, `repo_id`,
  `address`) to at most two GGUF repositories whose own Hugging Face card names this model's
  repository as the model they quantize, from the publishers its `jobs.yaml` entry allows (the
  model's own organisation first), with `observed_at` and `source`. Never matched by name, and it
  holds no file names, types or sizes; the reader opens the repository for those.
  `summary` is written for a newcomer: what the model is and what it is for, with no ranking words
  ("best", "strongest", "leading") unless the sentence names who said so.
- **organization** — `name`, `kind` (lab, company, foundation, community), `website`, `hf_handles[]`,
  `aliases[]`. Body section: Overview.
- **fine-tuning-method** — `method_type`, `memory_profile`, `summary`, `when_to_use` (one
  sentence), `related_hyperparameters[]` (IDs). A record is written in one of two forms:
  - **Schema 2.8 (the teaching form; write new and revised records this way).** The page is
    visual-first, in this order: the figure chosen by `visual` with the Definition under it → How
    It Works → Calculation → Worked Example → Memory → `typical_values` (drawn as Published
    settings) → Trade-offs → the comparison of all methods, built from every record's `profile`
    → `mistakes` → When To Use → `sources`. `typical_values` and `mistakes` have exactly the shape
    of the hyperparameter fields of the same names, and the consumer reads them with the same
    mapping.
  - **Schema 1.0 (earlier records).** Body sections Definition → What to look for → Calculation →
    Example; the page prints `when_to_use` first.
  - **`visual`** is computed, never drawn by hand; its inputs are the numbers the Worked Example
    uses, and a bar label is at most 14 characters.
    - `memory_terms`: bytes per weight of each term times `parameters`, in gigabytes, and the sum.
    - `adapter_size`: one `rows` × `columns` matrix against LoRA's r × (rows + columns) for each
      rank, and each pair's share of the matrix.
    - `memory_bars`: published memory figures copied from the source, `is_lower_bound` where it
      says "more than"; one panel per comparison.
    - `dpo_loss`: the implicit rewards β·(log π − log π_ref) of the chosen and the rejected answer,
      and the DPO loss of the pair against ln 2, its value while the policy equals the reference.
    - `orpo_loss`: each answer's probability e^(average token log-probability) and odds, the log
      odds ratio, and L_SFT + λ·L_OR.
    - `kl_reward`: ln(policy ÷ reference) per token and the score minus β times their sum, per β.
  - **`profile`** is the method's row of the comparison (`trains`, `models_in_training`, `data`,
    `stages`, `source`); each record holds only its own row, and the consumer assembles the table.
  - **Every number has its source.** Each `typical_values` row, `profile` row and mistake rests on
    an entry of `sources` fetched for the record; findings are attributed ("the authors report"),
    a rule of thumb is written only as the sum of sourced terms, and a made-up example says so.
    Prose follows the hyperparameter rules: plain words for a newcomer, a bold lead line then
    labelled bullets, every calculation step numbered with its result.
- **license** — `spdx_id`, `category`, a boolean `permissions` matrix, `conditions[]`,
  `limitations[]`, `models_using[]` (IDs). Body section: Plain-English.
- **quantization-topic** — one page of the Quantization guide, in the teaching form: `summary` (one
  plain sentence), `order` (reading order), then figures in `visuals` and sourced tables in
  `tables`. Body sections: What It Is first, What To Watch last when present; between them the
  page's own topics, in file order.
  - **A figure or table opens the section it names** in `section`: it is drawn first, then the
    section's text. A section takes at most one figure; a table naming a section that already has
    a figure, or a second table, is drawn after the section under its own `title`.
  - **Figures are computed, never drawn by hand.** `bit_layout` draws one square per bit of each
    format, coloured by `role` (`sign`, `exponent`, `mantissa`, `integer`). `affine_example`
    quantizes the example `weights` to `bits` unsigned levels with the affine formulas of the
    Hugging Face Transformers quantization concepts page (scale, zero point, quantize, dequantize)
    and prints every step. `model_memory` reads `total_parameters` from the `model` record and
    draws parameters × bits ÷ 8 for each precision. `name_decoder` splits names into parts, the
    example shape of the hardware repo's name decoders. Example values are named as examples in
    `caption`.
  - **Every row has its source.** Each table row names one entry of `sources`, fetched for the
    record; a measured number is copied with the source and the model it was measured on, and a
    computed one shows its formula in the cell or the text. No ranking words unless the sentence
    names who said them.

## Sourcing

Open-source-first, zero plagiarism. Prefer open papers, official model cards, and `config.json`.
Reference paywalled material only as a cited pointer, never as copied text. Record everything in
`sources[]`.
