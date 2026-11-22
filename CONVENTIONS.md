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
- `schema_version`: the `major.minor` the file was authored against (currently `2.4`).

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
  - **Figures are computed, never drawn by hand.** `visual.kind` is `schedule` (the learning rate
    at every step) or `distribution` (next-token probabilities at several temperatures); the
    consumer computes every point from the values in the record. A value that is an example and
    not a published one is named as such in `caption`.
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
- **parameter** — one file per architecture family (`arch`), with a ranked `components[]` list and a
  `worked_example.config` the consumer live-computes. Body explains each component + the master formula.
- **architecture-era** — carries `led_to[]` / `influenced_by[]` (IDs) forming the evolution DAG,
  plus `era`, `year`, `family`. Body sections: What Changed → Key Ideas.
- **model** — one record per base model. Carries the `fingerprint`, `organization` and `license`
  (IDs; `license_note` names a licence with no record), `variants[]` (quantised or repackaged copies:
  name, format, address, publisher), `aliases[]`, `hub_signals` and lineage (benchmark values live in `score-sheet` records of
  `atlas-evaluation-content`). Prose is
  optional: a record without `summary` has none yet. The prose queue writes up to five passages,
  each a field and a body section (`summary`, `intended_use`, `what_sets_it_apart`, `running_it`,
  `limitations`), with the span of the source behind each in `quotes`. Body sections, all optional:
  Summary → Intended Use → What Sets It Apart → Running It → Limitations → Why It Matters → Notes.
  A field with no source is absent, never `0` or `Unknown`; a passage the source cannot support has
  neither field nor section. `fingerprint.norm_placement` (`pre` | `post`) and `fingerprint.stack`
  (`encoder` | `decoder` | `encoder-decoder`) are read from the model's official implementation or
  paper, which is then listed in `sources`; a block with any other layout leaves both absent, and a
  consumer draws no block figure without them. `embedding_dimension` is the vector length an
  embedding model returns, copied from its card or pooling configuration (never from
  `fingerprint.hidden_size`). `base_models[]` (`name`, `address`) names the bases that have no
  record here, nearest first; a base with a record goes in `lineage.parents`. An arXiv entry of
  `sources` carries the paper's title as the arXiv API gives it, copied by code, not the bare ID.
  `summary` is written for a newcomer: what the model is and what it is for, with no ranking words
  ("best", "strongest", "leading") unless the sentence names who said so.
- **organization** — `name`, `kind` (lab, company, foundation, community), `website`, `hf_handles[]`,
  `aliases[]`. Body section: Overview.
- **fine-tuning-method** — `method_type`, `memory_profile`, `related_hyperparameters[]` (IDs).
  Body sections: Definition → What to look for → Calculation → Example.
- **license** — `spdx_id`, `category`, a boolean `permissions` matrix, `conditions[]`,
  `limitations[]`, `models_using[]` (IDs). Body section: Plain-English.

## Sourcing

Open-source-first, zero plagiarism. Prefer open papers, official model cards, and `config.json`.
Reference paywalled material only as a cited pointer, never as copied text. Record everything in
`sources[]`.
