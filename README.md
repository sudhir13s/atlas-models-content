# atlas-models-content

Portable, reusable reference knowledge about **AI models** — authored as Markdown with structured
YAML frontmatter, validated by JSON Schema, and readable by both humans and AI agents. This repo is
**content only**: no code, no build step, no framework lock-in. Any project can vendor it (as a git
submodule or a plain checkout) and render it however it likes.

It is the `models` domain of a seven-repo reference library
(`atlas-models-content`, `atlas-evaluation-content`, `atlas-frameworks-content`,
`atlas-hardware-content`, `atlas-news-content`, `atlas-practices-content`, `atlas-research-content`), all following the same
[content-repo standard](CONTENT-STANDARD.md).

## What's inside

| Type | Folder | What it captures |
|------|--------|------------------|
| `model` | `catalog/` | A base model: architecture fingerprint, parameters, license, lineage, and its quantised or repackaged copies as `variants`; prose is optional |
| `organization` | `organizations/` | Who creates models: name, kind, website, Hugging Face handles, aliases |
| `architecture-era` | `architectures/` | A rung on the 1950s→now evolution ladder: key innovation, representative models, lineage |
| `parameter` | `parameters/` | Structural knobs (layers, d_model, heads, vocab, context) per architecture family, with formulas |
| `hyperparameter` | `hyperparameters/` | Training + inference knobs, ranked by criticality, each with definition / calculation / example |
| `fine-tuning-method` | `fine-tuning-methods/` | LoRA, QLoRA, full FT, DPO, RLHF, ORPO — when to use, key knobs, worked examples |
| `license` | `licenses/` | Model licenses: permission matrix, conditions, limitations |

## Quick start

```bash
# Vendor it
git clone https://github.com/sudhir13s/atlas-models-content.git
# or as a submodule of your project
git submodule add https://github.com/sudhir13s/atlas-models-content.git content/models-src
```

Every content file is a Markdown file with YAML frontmatter (the structured fields) and a Markdown
body (prose, formulas, examples). Enumerate the library by globbing `**/*.md` and reading each
file's frontmatter, or start from [`meta.json`](meta.json) and
[`schema/registry.json`](schema/registry.json).

## Structure

```
meta.json                 # repo descriptor (machine entrypoint 1)
schema/registry.json      # type → folder/schema/sections map (machine entrypoint 2)
schema/*.schema.json      # JSON Schema per content type
templates/*.template.md   # copy these to author new content
catalog/ organizations/ architectures/ parameters/ hyperparameters/ fine-tuning-methods/ licenses/
assets/                   # SVG diagrams
```

## Contributing

See [CONTRIBUTING is folded into CONVENTIONS.md](CONVENTIONS.md) for the authoring rules, and
[AGENTS.md](AGENTS.md) for the machine-navigation guide. In short: copy a template, fill the
frontmatter and required sections, use IDs (not titles) for links, and open a PR. Content must be
open-source-sourced and cited — never copied from paywalled material.

## License

Content is licensed **CC-BY-4.0** (see [LICENSE](LICENSE)) — reuse freely with attribution.
