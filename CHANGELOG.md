# Changelog

All notable changes to `ai-models-content`. Grouped by schema version / release. Follows
[Keep a Changelog](https://keepachangelog.com/) conventions.

## [Unreleased]

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
