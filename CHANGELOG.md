# Changelog

All notable changes to `ai-models-content`. Grouped by schema version / release. Follows
[Keep a Changelog](https://keepachangelog.com/) conventions.

## [Unreleased]

### Schema 2.5 for `architecture-era`: the teaching form (additive)

- **Added** to `architecture-era`: `visual` (a `distribution` figure for the Worked Example),
  `comparison` (`before`, `after`, `rows[]` of `aspect`, `before`, `after`, `source`),
  `key_numbers` (`columns`, `rows[]` of `cells`, `source`) and `misunderstandings[]`
  (`misunderstanding`, `truth`, `source`); optional body sections How It Works and Worked Example
  after Key Ideas.
- **Added** `panel_titles` to the `distribution` figure: the title of each panel in order, for
  scores that are not next-token scores (router scores, attention scores).
- **Rewritten** in the 2.5 form, every fact from a source fetched for the rewrite: `transformer`
  and `mixture-of-experts`. `build_steps` and `eli5` removed from both (How It Works replaces
  them).
- **Fixed** on `mixture-of-experts`: the figure caption described two of five experts while the
  figure is DeepSeek-V3 (8 of 256 routed experts plus a shared one); the source Jacobs et al.
  (1991) added; Mixtral's year 2023 now rests on the Mistral AI announcement.
- **Fixed** on `transformer`: notable-model notes restated from their papers (GPT's 12-layer
  decoder-only stack, T5's text-to-text format); the two tutorial pages removed from `sources`
  (they stay under `resources`); the Vision Transformer limitation and the √d_k reason quoted
  from the papers.

### Schema 2.2 for `hyperparameter`: options, more computed figures (additive)

- **Added** `options[]` (`value`, `effect`) to `hyperparameter`: the values of a setting with no
  numeric scale, drawn as one tile per option in place of the axis. Such a record may omit
  `effects` and `scale`.
- **Added** to the `distribution` figure: `truncate` (`top_k`, `top_p` or `remove`) and `penalty`
  (`value`, `repeated`). A record sets exactly one of `temperatures`, `truncate` and `penalty`;
  `temperatures` is no longer required.
- **Added** the `beams` figure (`prompt`, `num_beams`, `tree`): two steps of beam search over a
  small table of probabilities.
- **Changed** the convention for `typical_values`: a `value` written as one plain number is also
  marked on the effect scale.
- **Rewritten** in the 2.2 form, every fact from a source fetched for the rewrite: `top-k`,
  `top-p`, `repetition-penalty`, `do-sample`, `num-beams`, `max-new-tokens`, `min-new-tokens`.

### Schema 2.4 for `model` (facts the detail page reads; additive)

- **Added** `embedding_dimension` (integer) to `model`: the vector length an embedding model
  returns, copied from its card or pooling configuration.
- **Added** `base_models[]` (`name`, `address`) to `model`: the bases that have no record here,
  nearest first. A base with a record stays in `lineage.parents`.
- **Changed** the convention for arXiv entries of `sources`: the title is the paper's own title
  as the arXiv API gives it, not the bare ID.
- **Set** on `bge-m3`: `summary` and `what_sets_it_apart` with their `quotes`,
  `total_parameters` (the tensor shapes of the published `pytorch_model.bin`, summed by code,
  because the Hub gives no safetensors count for this repository), `embedding_dimension`,
  `base_models`, `fingerprint.norm_placement` and `fingerprint.stack` (from the XLM-RoBERTa
  reference implementation), and the titles of its five papers.
- **Changed** on `qwen2-5-7b-instruct`: `summary` rewritten for a newcomer; `why_it_matters` and
  its body section removed (ranking claims with no source); `what_sets_it_apart` and `running_it`
  written from the model card with their `quotes`; `base_models` and paper titles set.
- **Fixed** `parameter/normalization` `controls`: it said the norm sits before each sub-layer,
  which is wrong for a post-norm block; it now names both placements.

### Schema 2.3 for `model` and `architecture-era` (block layout as record facts; additive)

- **Added** `fingerprint.norm_placement` (`pre` | `post`) and `fingerprint.stack` (`encoder` |
  `decoder` | `encoder-decoder`) to `model`. Both are set only from the model's official
  implementation or paper, and stay absent for any other layout (parallel sub-layers, a norm on
  both sides of a sub-layer, blocks that are not attention plus feed-forward).
- **Set** both fields on 79 catalog records, each from the implementation file now listed in the
  record's `sources`. Left unset on 16 records whose block is not a plain pre-norm or post-norm
  block: `phi-2`, `falcon-7b-instruct`, the three Pythia records, `gemma-2-9b-it`, `gemma-4-e4b-it`,
  `qwen3-8-27b`, `qwen3-8-flash-next`, `autotrust-decider-pro-35b`, `qwen3-coder-next-fp8`, the
  three Nemotron records, and the two Kimi records (text model code not published in the repository).
- **Added** `underlying_model_note` to `architecture-era`: the sourced sentence a consumer prints
  above a second figure, one catalog model's whole architecture. Written for `reasoning-models`
  and `transformer`.
- **Added** `architecture_id: architecture-era/vision-transformer` to `vit-base-patch16-224`, and
  the key feature "Two-layer MLP as the feed-forward sub-layer of each
  block" to `architecture-era/vision-transformer`, from the reference implementation.

### Schema 2.2 for `hyperparameter` (a fuller, visual-first record; additive)

- **Added** `effects` (`decrease[]`, `increase[]`: short phrases) and `scale` (`min`, `max`,
  `typical_min`, `typical_max`, `spacing`), which a consumer draws as one lower-to-higher scale.
- **Added** `typical_values[]` (`setting`, `value`, `source`): who used which value, each row
  linked to one entry of `sources`.
- **Added** `mistakes[]` (`mistake`, `why`, `fix`; 2 to 5).
- **Added** `visual`, the parameters of a figure the consumer computes: `kind: schedule` (`peak`,
  `warmup_steps`, `total_steps`, `curves`, optional `final_ratio` and `marks`) or
  `kind: distribution` (`tokens`, `logits`, `temperatures`); both carry a `caption`.
- **Added** the body sections How It Works, Worked Example and What To Watch.
- **Changed** the registry entry: only Definition is required; How It Works, What to look for,
  Calculation, Example, Worked Example and What To Watch are optional and keep that order. The
  schema 1.0 fields (`starting_value`, `if_increased`, `if_decreased`, `common_mistake`) and
  sections stay valid, so the 19 records not yet converted are unchanged.
- **Converted** `hyperparameter/learning-rate` and `hyperparameter/temperature` to 2.2 and
  rewrote both from sources read for this change. Removed because no source read supports them:
  square-root scaling as the "safe default", "drop 10 to 100 times" for fine-tuning, the
  gradient-norm guidance, the effective-batch formula, the Kaplan et al. citation, and the
  per-task temperature bands (0.0 to 0.3 factual, 0.7 general, 1.0 to 1.2 creative).
- **Changed** the template and CONVENTIONS to the 2.2 form: visual-first page order, plain-language
  depth, calculations a reader can redo by hand.

### Accuracy pass over the teaching records (content only; no schema change)

- **Fixed** attributions, definitions, dates and example-model lists in all `concepts/`,
  `architectures/` and `milestones/` records so each statement rests on a cited primary source
  (the introducing paper, an official model card or config). Statements no source supports were
  removed or reduced to what the source states; superlatives and influence claims were removed.
- **Fixed** origins credited to a model that popularised a technique instead of the paper that
  introduced it (for example mixture-of-experts now cites Shazeer et al. 2017, diffusion cites
  Sohl-Dickstein et al. 2015, grouped-query attention cites Ainslie et al. 2023).
- **Fixed** the 2025 milestone title: Phi-4 was removed because its technical report dates from
  December 2024.
- **Added** source links to every milestone and concept record.

### Schema 2.1 for `model` (a fuller entry written by the prose queue; additive)

- **Added** four prose fields beside `summary`: `intended_use`, `what_sets_it_apart`,
  `running_it`, `limitations`. Each is absent when the model card does not support it; the word
  `unknown` is never stored.
- **Added** `quotes`, a mapping from each written passage's field name to the one span of the
  model card it rests on. It replaces the single `quote`, which stays in the schema for the
  records written under writer prompt 1.0.0.
- **Added** the body sections Intended Use, What Sets It Apart, Running It and Limitations to the
  model's `optional_sections`, in the order Summary, Intended Use, What Sets It Apart, Running It,
  Limitations, Why It Matters, Notes. Summary and the four new sections are written by code from
  the passages; Why It Matters and Notes stay for hand-written records.
- **Changed** `why_it_matters`: the prose queue no longer writes it; records that have it keep it.

### Schema 2.0 for `model` (scores leave the model record; breaking)

- **Removed** `model.benchmarks[]`. Benchmark values now live only in `score-sheet` records of
  `atlas-evaluation-content` (`score-sheet/<model-slug>`), one entry per benchmark and source, and
  the database reads them from there (`benchmark_scores`). A `model` file that still carries
  `benchmarks` is a schema violation.
- **Converted** the seven records that held it (DeepSeek-V3, Gemma 2 9B, Llama 3.1 8B, Mixtral 8x7B,
  Phi-3 mini, Qwen2.5 7B, Qwen2.5 72B); every value had already been moved to a score sheet.
- The `task.benchmarks` list (benchmark IDs a task is ranked on) is unchanged.

### Schema 1.2 (architecture content leaves the frontend; additive)

- **Added** the `concept` type (`concepts/`, `concept/<slug>`): `category` (attention,
  positional-encoding, normalization, activation), `code` (the fingerprint value), `aliases`,
  `order`, `short_description`, `example_models`; body sections What It Is, Why It Matters,
  Trade-off. 11 records, copied word for word from `lib/architecture-concepts.ts`.
- **Added** the `milestone` type (`milestones/`, `milestone/<year>-<slug>`): `year`, `order`,
  `architectures[]` (`architecture-era/<slug>`), `models[]` (`model/<slug>`); optional body section
  Summary. 10 records, copied from the Evolution page's Key Milestones card; a name with no record
  is left out of the lists, never invented.
- **Added** to `architecture-era`: `variants[]` (`name`, `subtitle`, `models`), set on
  `architecture-era/transformer` from the LLM fingerprints page's sub-architecture list.
- **Added** `concept` and `milestone` to the shared `type` enum, their templates, and
  `meta.json` / `registry.json` entries; both schemas reject unknown fields.

### Schema 1.1 (migration of the Hugging Face model snapshot)

- **Added** the `organization` type (`organizations/`, `organization/<slug>`): `name`, `kind`
  (lab, company, foundation, community), `website`, `hf_handles`, `aliases`; body section Overview.
- **Added** to `model`: `variants[]` (`name`, `format`, `address`, `publisher`), `aliases[]`,
  `license_note`, `filters.library`, `hub_signals.last_modified` and `hub_signals.source`
  (`hub_signals` now requires `observed_at` and `source`).
- **Added** the shared optional `locked` field (field paths no job may change) to every type.
- **Changed** `model.organization` and `model.license` to IDs (`organization/<slug>`,
  `license/<slug>`); the eight curated records were converted.
- **Changed** `model.summary` to optional (absent means the record has no prose yet) and every
  `model` body section to optional (registry `optional_sections`); `model` needs `license` or
  `license_note`.
- **Added** six licence records: Llama 3.2, Llama 3, Llama 2, BigScience BLOOM RAIL 1.0, Apple AMLR,
  BigCode OpenRAIL-M.
- **Added** 71 `model` records (one per Hugging Face base model of the snapshot, built by code from
  Hugging Face facts, 16 quantised or repackaged copies listed as `variants`) and the 19
  `organization` records they point to; the eight curated records were merged, not rewritten.
- **Changed** `model.fingerprint`: only `num_layers` and `hidden_size` are required, and a gated
  repository whose `config.json` cannot be read may have no fingerprint.
- **Added** to `model` (still schema 1.1, additive): `quote`, `review` and `generated_by`, the
  provenance of prose the prose queue (`backfill` job) writes, in the shape a feed item records its
  own. `summary` and `why_it_matters` are written by the same job; `why_it_matters` stays absent
  when the model card states no reason.

### Added
- Repository scaffold to the content-repo standard: `meta.json`, `schema/registry.json`, the seven
  JSON Schemas (`_shared`, `model`, `architecture-era`, `parameter`, `hyperparameter`,
  `fine-tuning-method`, `license`), templates, and docs (`README`, `AGENTS`, `CONVENTIONS`).
- Initial LLM-first content library (schema version 1.0), 67 files, all cited and cross-link-validated:
  - **8 model cards** — Llama-3.1-8B, Mistral-7B-v0.3, Qwen2.5-7B/72B, Gemma-2-9B, Mixtral-8x7B,
    Phi-3-mini, DeepSeek-V3 — with fingerprints verified against Hugging Face `config.json`.
  - **21 architecture eras** — perceptron → reasoning models, with resolvable `led_to`/`influenced_by`
    lineage for the evolution graph.
  - **21 hyperparameters** — training (12) + inference (7) + the two seeds, each criticality-ranked
    (`priority`) with Definition / What to look for / Calculation / worked Example.
  - **5 parameter families** — LLM/transformer, MoE, SSM/Mamba, CNN, RNN/LSTM — each with a master
    formula and a worked numeric example.
  - **6 fine-tuning methods** — LoRA, QLoRA, full fine-tuning, DPO, RLHF/PPO, ORPO.
  - **6 licenses** — Apache-2.0, MIT, Llama-3.1, Gemma, Qwen, DeepSeek — with permission matrices.

## Schema versions

- **1.0** — initial schema for all six model content types.
- **1.1** — `organization` type, model `variants`/`aliases`/`license_note`, IDs for `organization`
  and `license`, optional prose, shared `locked`.
- **1.2** — `concept` and `milestone` types, `architecture-era.variants`.
- **2.0** — `model.benchmarks` removed; scores live in `score-sheet` records.
- **2.1** — `model` prose passages (`intended_use`, `what_sets_it_apart`, `running_it`,
  `limitations`) and `quotes`.
- **2.2** — `hyperparameter` `effects`, `scale`, `typical_values`, `mistakes`, `visual`; sections
  How It Works, Worked Example, What To Watch.
