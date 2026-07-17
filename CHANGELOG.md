# Changelog

All notable changes to `ai-models-content`. Grouped by schema version / release. Follows
[Keep a Changelog](https://keepachangelog.com/) conventions.

## [Unreleased]

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
