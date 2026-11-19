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
- `schema_version`: the `major.minor` the file was authored against (currently `1.1`).

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
  Body sections: Definition → What to look for → Calculation → Example.
- **parameter** — one file per architecture family (`arch`), with a ranked `components[]` list and a
  `worked_example.config` the consumer live-computes. Body explains each component + the master formula.
- **architecture-era** — carries `led_to[]` / `influenced_by[]` (IDs) forming the evolution DAG,
  plus `era`, `year`, `family`. Body sections: What Changed → Key Ideas.
- **model** — one record per base model. Carries the `fingerprint`, `organization` and `license`
  (IDs; `license_note` names a licence with no record), `variants[]` (quantised or repackaged copies:
  name, format, address, publisher), `aliases[]`, `hub_signals` and lineage (benchmark values live in `score-sheet` records of
  `atlas-evaluation-content`). Prose is
  optional: a record without `summary` has none yet. Body sections, all optional: Summary → Why It
  Matters → Notes. A field with no source is absent, never `0` or `Unknown`.
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
