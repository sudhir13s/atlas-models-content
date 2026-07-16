# Changelog

All notable changes to `ai-models-content`. Grouped by schema version / release. Follows
[Keep a Changelog](https://keepachangelog.com/) conventions.

## [Unreleased]

### Added
- Repository scaffold to the content-repo standard: `meta.json`, `schema/registry.json`, the seven
  JSON Schemas (`_shared`, `model`, `architecture-era`, `parameter`, `hyperparameter`,
  `fine-tuning-method`, `license`), templates, and docs (`README`, `AGENTS`, `CONVENTIONS`).
- Seed content (schema version 1.0):
  - `catalog/llama-3-1-8b-instruct.md` — gold-standard model card with a full, cited fingerprint.
  - `architectures/transformer.md`, `architectures/mixture-of-experts.md` — evolution eras.
  - `parameters/llm-transformer.md` — structural parameters + master formula + worked example.
  - `hyperparameters/learning-rate.md`, `hyperparameters/temperature.md` — flagship ranked knobs.
  - `fine-tuning-methods/lora.md` — worked LoRA example.
  - `licenses/apache-2-0.md`, `licenses/llama-3-1.md` — license entries.

## Schema versions

- **1.0** — initial schema for all six model content types.
