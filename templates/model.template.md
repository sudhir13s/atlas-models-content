---
id: model/<slug>
type: model
slug: <slug>
schema_version: 1.1
title: "<Human Readable Model Name>"
organization: organization/<organization-slug>
release_date: "<YYYY-MM-DD>"
release_year: <YYYY>
release_month: <M>
license: license/<license-slug>   # or `license_note: "<licence name as the source gives it>"` when no license record exists
signal_strength: medium          # high | medium | low
architecture_id: architecture-era/<slug>
model_category: language
parameters_active_b: <number>
total_parameters: <int>
context_window_tokens: <int>
embedding_dimension: <int>       # embedding models only: the vector length the card or pooling configuration states
base_models:                     # bases with no record here, nearest first; a base with a record goes in lineage.parents
  - { name: "<owner/repo>", address: "https://huggingface.co/<owner/repo>" }
summary: "<2-3 sentences: what the model is, its family or base model, what it was built to do>"   # omit the five passages, quotes and the body for a record with no prose yet
intended_use: "<1-3 sentences: the tasks and users the source names, and uses it rules out>"   # a passage the source cannot support is omitted, never written as "unknown"
what_sets_it_apart: "<1-3 sentences: the design choice, training approach or reported result that separates it from its siblings or predecessors>"
running_it: "<1-2 sentences: recommended settings, prompt format, hardware, what must or must not be done>"
limitations: "<1-2 sentences: what the source says the model does badly, does not cover, or risks>"
why_it_matters: "<one sentence on why this model matters>"   # hand-written records only; the prose queue does not write it
quotes:                          # set by the prose queue: one span of the source per written passage
  summary: "<span of the source text>"
  intended_use: "<span of the source text>"
  what_sets_it_apart: "<span of the source text>"
  running_it: "<span of the source text>"
  limitations: "<span of the source text>"
fingerprint:
  attention_type: <MHA | GQA | MQA | MLA>
  num_layers: <int>
  attention_heads: <int>
  kv_heads: <int>
  head_size: <int>
  hidden_size: <int>
  intermediate_size: <int>
  context_window_tokens: <int>
  vocabulary_size: <int>
  activation_function: <SwiGLU | GELU | ...>
  normalization: <RMSNorm | LayerNorm>
  norm_placement: <pre | post | both>   # only when read from the paper or official implementation; absent for any other layout
  stack: <encoder | decoder | encoder-decoder>   # same rule
  parallel_sublayers: <shared-norm | separate-norms>   # only when attention and feed-forward both read the block's input (with norm_placement: pre)
  output_head: <cell-selection | cell-selection-aggregation>   # table models only, as the implementation names the head
  layer_mixers: { names: ["<mixer>", "<mixer>"], pattern: "<how they are spread over the layers>", note: "<one to three sentences>", feed_forward: true }   # only when layers hold different mixers
  decoder_layers: <int>            # encoder-decoder only: the decoder's block count (num_layers is the encoder's)
  embedding_norm: true             # only when the implementation normalises the summed embeddings before the first block
  input_stem: <spectrogram-convolution | spectrogram-patches | waveform-convolution>   # audio models only, read from the implementation
  positional_encoding: <RoPE | ALiBi | learned>
  decoder_positional_encoding: <Learned | ...>   # encoder-decoder only, when it differs from the encoder's
  rope_theta: <number | null>
  parameters_active_b: <number>
  kv_cache_rating: <low | medium | high>
  key_detail: "<notable detail>"
pipeline:                        # the whole model as stages, only when one primary source states the structure
  source: "<the url of the entry of sources the structure was read from>"
  summary: "<two or three plain sentences on what the figure shows>"
  side_into: <id of the main-column stage the side stages feed>   # only with side stages
  stages:                        # top to bottom; a stage the source does not state is left out
    - { id: <slug>, role: input, title: "<at most 28 characters>", details: ["<at most 36 characters>"], note: "<one or two sentences>" }
    - { id: <slug>, role: visionEncoder, title: "<...>", frame: "<group name>", repeat: <int> }   # consecutive stages of one frame share it
    - { id: <slug>, role: tokenizer, title: "<...>", side: true }
    - { id: <slug>, role: textEncoder, title: "<...>", side: true, into: <id> }   # `into` starts a further side run that feeds <id>
    - { id: <slug>, role: denoiser, title: "<...>", frame: "<loop>", inner_frame: "<block>", inner_repeat: <int> }   # a repeated block inside a loop
    - { id: <slug>, role: outputHead, title: "<...>", branch: true }   # a second output, leaving from pipeline.branch_from
moe_config: null                 # or { expert_count, active_experts, shared_expert, notes }
filters:
  size_category: <tiny | small | medium | large | xlarge>
  is_quantized: false
  quant_format: original
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
  attention_type: <...>
hub_signals:                     # owned by the jobs; numbers that change
  downloads_30d: <int>
  likes: <int>
  last_modified: <YYYY-MM-DD>
  observed_at: <YYYY-MM-DD>
  source: "<url>"
gguf_repositories:               # owned by the jobs; links to GGUF copies whose own card names this model as their base
  repositories:
    - { publisher: <hf-handle>, repo_id: "<owner/repo>", address: "https://huggingface.co/<owner/repo>" }
  observed_at: <YYYY-MM-DD>
  source: "https://huggingface.co/models?library=gguf&other=base_model:quantized:<owner/repo>"
variants:                        # quantised or repackaged copies, one entry each
  - { name: "<owner/repo>", format: awq, address: "https://huggingface.co/<owner/repo>", publisher: <hf-handle> }
aliases: ["<owner/repo>"]
lineage:
  parents: [model/<base-slug>]
  children: []
locked: [summary, why_it_matters]  # field paths no job may change (any passage field, or `body`)
tags: [<tag>, <tag>]
updated: <YYYY-MM-DD>
sources:
  - { url: "<url>", title: "<title>", trust: 0.9 }
  - { url: "https://arxiv.org/abs/<id>", title: "<the paper's title as arXiv gives it>", trust: 0.95 }
related: [architecture-era/<slug>]
---

## Summary
<2-3 sentences: what the model is.>

## Intended Use
<1-3 sentences: tasks and users, and uses ruled out.>

## What Sets It Apart
<1-3 sentences: what separates it from its siblings, base model or predecessors.>

## Running It
<1-2 sentences: settings, prompt format, hardware.>

## Limitations
<1-2 sentences: what it does badly, does not cover, or risks.>

## Why It Matters
<1-2 paragraphs on the model's significance; hand-written records only.>

## Notes
<Notable configuration details, gotchas, deployment notes.>
