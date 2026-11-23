---
id: model/<slug>
type: model
slug: <slug>
schema_version: 3.0              # the model schema's current version; the schema refuses any other
title: "<Human Readable Model Name>"
organization: organization/<organization-slug>
release_date: "<YYYY-MM-DD>"     # the year and month are read from it, never stored beside it
license: license/<license-slug>   # or `license_note: "<licence name as the source gives it>"` when no license record exists
signal_strength: medium          # high | medium | low
architecture_id: architecture-era/<slug>
model_category: language         # what the model is for: language | vision | audio | multimodal | generation | retrieval | documents | structured-data | robotics-and-control
model_kind: transformer-language # how it is built: transformer-language | transformer-vision | multimodal | convolutional | diffusion | audio-speech | embedding | tabular-time-series | control-policy; omit when unsure, never guess
parameters_active_b: <number>    # stored here only, never inside fingerprint
total_parameters: <int>
context_window_tokens: <int>     # stored here only, never inside fingerprint; not on a convolutional model
embedding_dimension: <int>       # model_kind embedding only: the vector length the card or pooling configuration states
base_models:                     # bases with no record here, nearest first; a base with a record goes in lineage.parents
  - { name: "<owner/repo>", address: "https://huggingface.co/<owner/repo>" }
summary: "<2-3 sentences: what the model is, its family or base model, what it was built to do>"   # omit the five passages and quotes for a record with no prose yet; a passage is written here only, never repeated in the body
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
fingerprint:                     # a transformer stack; not on a convolutional model
  attention_type: <MHA | GQA | MQA | MLA>   # stored here only; `filters` has no copy
  num_layers: <int>
  attention_heads: <int>
  kv_heads: <int>
  head_size: <int>
  hidden_size: <int>
  intermediate_size: <int>
  vocabulary_size: <int>
  activation_function: <silu | gelu | gelu_new | relu | SwiGLU | ...>   # one spelling per function: silu (not swish), gelu_new (not gelu_pytorch_tanh)
  normalization: <RMSNorm | LayerNorm>
  norm_placement: <pre | post | both>   # only when read from the paper or official implementation; absent for any other layout
  stack: <encoder | decoder | encoder-decoder>   # same rule
  parallel_sublayers: <shared-norm | separate-norms>   # only when attention and feed-forward both read the block's input (with norm_placement: pre)
  output_head: <cell-selection | cell-selection-aggregation>   # table models only, as the implementation names the head
  layer_mixers: { names: ["<mixer>", "<mixer>"], pattern: "<how they are spread over the layers>", note: "<one to three sentences>", feed_forward: true }   # only when layers hold different mixers
  decoder_layers: <int>            # encoder-decoder only: the decoder's block count (num_layers is the encoder's)
  embedding_norm: true             # only when the implementation normalises the summed embeddings before the first block
  input_stem: <spectrogram-convolution | spectrogram-patches | waveform-convolution>   # audio models only, read from the implementation
  positional_encoding: <RoPE | Learned | ALiBi | Sinusoidal | Convolutional>
  decoder_positional_encoding: <same values>   # encoder-decoder only, when it differs from the encoder's
  rope_theta: <number>
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
moe_config: { expert_count: <int>, active_experts: <int>, shared_expert: true, notes: "<...>" }   # only with filters.is_moe: true; omit otherwise, never null
filters:
  size_category: <tiny | small | medium | large | xlarge>
  is_quantized: false
  quant_format: original
  is_moe: false                  # the one stored statement: true = mixture of experts, false = its configuration names no experts; omit when no source says and on a convolutional model
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
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
tags: [<model type such as bert or vit>]   # only what no field states: never the pipeline tag, the attention type, `llm`, `moe` or `dense`
updated: <YYYY-MM-DD>
sources:
  - { url: "<url>", title: "<title>", trust: 0.9 }
  - { url: "https://arxiv.org/abs/<id>", title: "<the paper's title as arXiv gives it>", trust: 0.95 }
related: [architecture-era/<slug>]
---

## Notes
<Hand-written records only: notable configuration details, gotchas, deployment notes. The body never repeats a frontmatter passage; most records have no body at all.>
