# Changelog

All notable changes to `ai-models-content`. Grouped by schema version / release. Follows
[Keep a Changelog](https://keepachangelog.com/) conventions.

## [Unreleased]

### Schema 3.0 for `model`: every fact once, real kinds, strict schema (breaking)

All 188 `model` records were converted by the consumer's `atlas migrate model-restructure`. The
conversion only moved, renamed, derived or deleted: no value was added that a record did not hold,
and `updated` was left alone because no fact changed.

- **Removed** `fingerprint.context_window_tokens` (109 copies of the top-level field; on
  `gemma-2-2b-it` and `gemma-2-27b-it` it was the only copy and moved up),
  `fingerprint.parameters_active_b` (8 copies), `filters.attention_type` (117 copies of
  `fingerprint.attention_type`), `release_year` and `release_month` (167 each, read from
  `release_date`), `moe_config: null` (6 records; absent now) and the unused `quote`.
- **Removed** 552 tags that only repeated a field: the pipeline tag, the attention type, `llm`,
  `moe` and `dense`. `tags` now hold only what no field states.
- **Removed** 628 body sections that were word-for-word copies of the frontmatter passages
  (`summary`, `intended_use`, `what_sets_it_apart`, `running_it`, `limitations`). The registry's
  optional sections of a model are now Summary, Why It Matters and Notes: the hand-written
  long-form text eight records keep, which is not in their frontmatter.
- **Changed** `locked` on 29 records: a lock on a removed path names the surviving one
  (`fingerprint.context_window_tokens` → `context_window_tokens`, `fingerprint.parameters_active_b`
  → `parameters_active_b`, `filters.attention_type` → `fingerprint.attention_type`, `release_year`
  and `release_month` → `release_date`), each path once. The lock on `benchmarks` (7 records) is
  dropped: the field left the model record in schema 2.0.
- **Added** `model_kind` (how the model is built): `transformer-language` (108),
  `transformer-vision` (13), `multimodal` (16), `convolutional` (3), `diffusion` (15),
  `audio-speech` (16), `embedding` (6), `tabular-time-series` (9), `control-policy` (2). A fixed
  rule over each record's own fields placed 159; 29 were placed after reading the record. The
  schema's per-kind rules: a `transformer-language` record states its `fingerprint` unless it is
  gated; a `convolutional` record has no `fingerprint`, `moe_config`, `context_window_tokens` or
  `filters.is_moe` (the stored `is_moe: false` of `rmbg-1-4` and `superpoint` is removed); only an
  `embedding` record has `embedding_dimension`; `moe_config` needs `filters.is_moe: true`; only an
  `audio-speech` record has `fingerprint.input_stem`; `fingerprint.decoder_layers` and
  `decoder_positional_encoding` need `stack: encoder-decoder`.
- **Changed** to enums: `model_category` (its nine values), `fingerprint.attention_type`,
  `normalization`, `positional_encoding` and `decoder_positional_encoding`. `release_date` must be
  `YYYY-MM-DD`; `architecture_id` and `lineage` entries must be IDs; `rope_theta` is a number,
  never null; `moe_config` is an object, never null.
- **Changed** `fingerprint.activation_function` to one spelling per function: `swish` → `silu`
  (1 record) and `gelu_pytorch_tanh` → `gelu_new` (1 record). `situ` on `kimi-k3` is kept: the
  model's `config.json` states `hidden_act: situ`.
- **Changed** `schema_version` to a constant of the model schema (`3.0`): a model record with any
  other value is refused. All 188 records carry it (they said `1.1`).
- **Changed** to strict: no unknown field at the top level of a model record or inside
  `fingerprint`, `moe_config`, `filters`, `hub_signals`, `lineage` and `pipeline`.
- **Consumers must** read the context window and active parameters from the top level, the
  attention type from `fingerprint`, the year and month from `release_date`, mixture of experts
  from `filters.is_moe`, and the passages from the frontmatter.

### Schema 2.9 for `parameter`: the teaching form (additive)

- **Added** to `parameter` the hyperparameter teaching fields by reference (`effects`, `scale`,
  `typical_values`, `mistakes`) and `visual`, a figure computed from a real model's configuration
  (`decoder_config`) of one of five kinds: `count_sweep`, `count_breakdown`, `head_grid`,
  `kv_cache` and `context_cost`. Optional body sections How It Works, Worked Example and What To
  Watch; Definition stays the one required section, so 1.0 records remain valid. Schema, registry,
  template and conventions ship together; the conventions' old "parameter" entry, which described
  `parameter-calculator`, is renamed to it.
- **Rewritten** in the 2.9 form, every fact from a source fetched for the rewrite: `hidden-size`,
  `num-hidden-layers`, `num-attention-heads`, `head-dim`, `intermediate-size-ffn`,
  `context-length` and `vocab-size`. Worked examples use Qwen3-8B's config.json and reproduce its
  published total of 8,190,735,360 parameters exactly.
- **Fixed** on `num-attention-heads` and `head-dim`: "Q and O stay d² whatever the head count" and
  head_dim = hidden_size ÷ heads hold only when the configuration sets no head_dim; Qwen3-32B
  (5120 against 64 × 128) and Qwen3-0.6B (1024 against 16 × 128) are the sourced exceptions.
- **Fixed** on `context-length`: max_position_embeddings is a limit, not the usable context; the
  page now gives Qwen3-8B's 40,960 (config), 32,768 native and 131,072 with YaRN (card). GPT-2's
  learned position table is removed (the GPT-2 paper does not state it); BERT's 512 × 768 replaces
  it.
- **Removed** statements no fetched source supports: head_dim "far below 64 under-performs, far
  above 128 has diminishing returns", head_dim held at 64 to 128 "for compute efficiency",
  "very shallow models plateau on hard reasoning", "~70% of a modern LLM" as a general share,
  Llama 2's 32k vocabulary, GPT-2 small's 12 heads, and the KV cache at 128k being "far larger
  than the model's activations".
- **Added** to `parameter`: `options` (the hyperparameter definition, by reference) for a switch or
  a named choice, and seven `visual` kinds for convolutional and recurrent networks, defined in
  `schema/_parameter-cnn-rnn-visuals.schema.json`: `conv_geometry` (output size and the sliding
  window under stride, padding and dilation, on number grids), `conv_params` (k × k × in × out
  plus biases, summed per part), `pooling` (max and average windows), `batch_norm` (one channel
  normalised over a mini-batch), `recurrent_sweep` (one layer's count against hidden_size per
  cell), `recurrent_layers` (each layer of a stack, one or two directions) and `gate_blocks` (a
  layer split into its weight blocks).
- **Rewritten** in the 2.9 form, every fact from a source fetched and read for the rewrite (LeNet-5,
  AlexNet, VGG, ResNet, Batch Normalization, Yu and Koltun, DeepLab, Wang et al., Dumoulin and
  Visin, Springenberg et al., Network In Network, Hochreiter and Schmidhuber, Gers et al. 2002,
  Cho et al., Chung et al., Sutskever et al., Bahdanau et al., Schuster and Paliwal, the PyTorch
  documentation and torchvision source): `cnn-kernel-size`, `cnn-conv-stride`, `cnn-padding`,
  `cnn-dilation`, `cnn-num-filters`, `cnn-num-conv-layers`, `cnn-pooling`, `cnn-batch-norm`,
  `rnn-hidden-units`, `rnn-num-layers`, `rnn-cell-type`, `rnn-gates` and `rnn-bidirectional`.
  The VGG counts reproduce torchvision's totals (138,357,544 for VGG-16) and the paper's Table 2;
  Sutskever et al.'s 4 layers of 1,000 cells reproduce their 32M recurrent weights per LSTM.
- **Fixed** on the convolutional pages: ResNet-50 has 49 convolutions on its main path, not 50;
  VGG-19 has 16 convolutional layers, not 19; deep plain stacks degrade for reasons the ResNet
  authors trace to optimisation, not vanishing gradients; pooling has parameters in LeNet-5's
  sub-sampling layers (2 per map); bidirectional layers more than double a stack, because the
  layers above read both directions.
- **Fixed** on the recurrent pages: Cho et al. describe the GRU as having two gating units, not
  three; the three or four figures are weight blocks. The bias convention of every count is
  stated (PyTorch keeps two bias vectors per block, papers often none).
- **Removed** statements no fetched source supports: typical hidden sizes "128–256, 512–1024,
  2048", "deep-but-narrow stacks were often preferred", gains that "fall off past ~4 layers", "LSTM
  handles long-range dependencies best", "past ~20 layers plain stacks stop improving (vanishing
  gradients)", early and deep channel ranges "16–64" and "256–512", batch normalization "smooths
  the loss landscape" and is "standard in every modern CNN", GroupNorm and LayerNorm "for tiny
  batches", "valid padding is used when boundary effects don't matter", and the CS231n and Olah
  pages as sources (not primary).
- **Added** to `parameter` six `visual` kinds for transformer functions and mixtures of experts,
  defined in `schema/_parameter-transformer-visuals.schema.json`: `activation_curves` (relu, gelu,
  gelu_new and silu as Transformers computes them), `norm_compare` (one vector through LayerNorm
  and RMSNorm), `rope_rotation` (a query and key turned by one rotary pair of a real model's head),
  `moe_sweep` (total and active parameters as one expert value changes), `moe_count` (total
  against active, part by part) and `expert_layer` (one layer's routed and shared experts).
- **Rewritten** in the 2.9 form, every fact from a source fetched and read for the rewrite (GQA,
  MQA, RoFormer, ALiBi, GELU, Swish, SiLU, GLU variants, LayerNorm, RMSNorm, Press and Wolf,
  Shazeer et al. 2017, Switch Transformer, Mixtral, DeepSeekMoE, DeepSeek-V3, the Qwen3 report,
  Transformers and PyTorch source, each model's config.json): `num-key-value-heads-gqa`,
  `tied-embeddings`, `token-embedding`, `output-head`, `positional-encoding`,
  `activation-function`, `normalization`, `moe-num-experts`, `moe-top-k`, `moe-expert-ffn-size`
  and `moe-shared-experts`. The counts reproduce the published totals of Qwen3-4B, Qwen3-32B,
  SmolLM2-135M, Mistral-7B-v0.1, Qwen3-30B-A3B (30,532,122,624 total, 3,353,032,704 active) and
  Mixtral-8x7B (46,702,792,704 and 12,879,925,248).
- **Changed** `name` to a real configuration key: `norm_type` (no such key) to `rms_norm_eps`,
  `position_embedding_type` to `rope_theta`, `num_local_experts` to `num_experts`.
- **Removed** statements no fetched source supports, among them "small models almost always tie",
  "large models often untie (Llama 3, GPT-3)", tying as "a mild regularizer", "modern LLMs almost
  universally use RoPE", RoPE and ALiBi "extrapolate to longer contexts", SwiGLU's "quality per
  FLOP" and a "2.67×" intermediate size, RMSNorm "with equal or better stability", "extra norms
  rarely help", "16 experts would reach ~90B", top-2 as "the common sweet spot", and every Llama 3
  8B number (gated configuration).

### Schema 2.8 for `fine-tuning-method`: the teaching form (additive)

- **Added** to `fine-tuning-method`: `typical_values` and `mistakes` (the hyperparameter
  definitions, by reference), `profile` (the method's row of the comparison across all methods:
  `trains`, `models_in_training`, `data`, `stages`, `source`) and `visual`, a computed figure of
  one of six kinds: `memory_terms`, `adapter_size`, `memory_bars`, `dpo_loss`, `orpo_loss` and
  `kl_reward`. Optional body sections How It Works, Worked Example, Memory, Trade-offs and When
  To Use; Definition stays the one required section, so 1.0 records remain valid. Schema,
  registry, template and conventions ship together.
- **Rewritten** in the 2.8 form, every fact from a source fetched and read for the rewrite:
  `full-fine-tuning`, `lora`, `qlora`, `dpo`, `orpo` and `rlhf-ppo`.
- **Fixed** on `full-fine-tuning`: the bytes per weight of mixed-precision Adam listed four terms
  (2 + 4 + 4 + 4, which is 14) yet gave 16; ZeRO's accounting has five (16-bit weights and
  gradients, 2 + 2, and 32-bit weights, momentum and variance, 4 + 4 + 4). The Lester et al.
  source, which no statement used, is removed.
- **Fixed** on `lora`: A and B were given swapped shapes (the paper's B is d × r and A is r × k),
  and the example counted Llama 3 8B's value projection as 4096 × 4096 although it is smaller
  (grouped-query attention); the example now uses the paper's GPT-3 matrices.
- **Removed** statements no fetched source supports, among them a "solid default" rank of 8 to 16,
  lora_alpha "commonly 2 × r", learning-rate bands for LoRA, DPO and full fine-tuning, a DPO β of
  0.3 to cure terse answers, ORPO's memory as half of DPO's and a quarter of PPO's, PPO's
  learning rate "~1e-6", and the opinion "powerful but finicky".

### Schema 2.7 for `architecture-era`: a computed figure for every Worked Example (additive)

- **Added** twelve kinds of `visual` beside `distribution`, each naming the computation of a
  Worked Example and carrying its inputs: `recurrent_memory` (a plain recurrent network beside
  an LSTM cell, and the error left after n steps), `convolution` (filter, ReLU, max-pooling),
  `noise_schedule` (DDPM's linear schedule), `residual_gradient` (wⁿ against (1 + w)ⁿ),
  `discriminator` (the best GAN discriminator), `rotary` (rotary position embedding),
  `selective_scan` (a one-number state space model, selective against a fixed step size),
  `loss_formula` (the fitted L(N, D) of Hoffmann et al.), `group_advantages` (GRPO),
  `perceptron_training` (the perceptron learning rule), `backprop` (one forward and backward
  pass) and `gaussian_code` (a variational autoencoder's code against its prior). The consumer
  computes every value it draws from these inputs. Schema, template and conventions ship
  together.
- **Added** a `visual` to `rnn-lstm`, `cnn`, `diffusion`, `resnet`, `gan`, `llama-recipe`,
  `mamba-ssm`, `scaling-laws`, `reasoning-models`, `perceptron`, `mlp-backprop` and `vae`, with
  exactly the inputs of each record's Worked Example; no other fact changed.

### Schema 2.7 for `model`: links to GGUF copies (additive)

- **Added** the agent-owned block `gguf_repositories` to `model`: `repositories[]` (`publisher`,
  `repo_id`, `address`), `observed_at` and `source`. `models-scout` writes it by code: a GGUF
  repository whose own Hugging Face card names the model's repository as the model it quantizes,
  from an allowed publisher (the model's own organisation, then `unsloth`, `bartowski`,
  `ggml-org`), at most two per model. Links only: no file names, types or sizes. Schema,
  template and conventions ship together.

### Schema 2.7 for `model`: architecture figure fields (additive)

- **Added** to `fingerprint`: `decoder_layers` (an encoder-decoder's decoder depth),
  `embedding_norm` (a norm on the summed embeddings before the first block), `input_stem`
  (`spectrogram-convolution`, `spectrogram-patches`, `waveform-convolution`) and
  `decoder_positional_encoding`. Each is read from the model's configuration and the official
  implementation of its model type, which the record lists in `sources`.
- **Added** the block `pipeline` to `model`: the whole model as ordered `stages` (`id`, `role`,
  `title`, `details[]`, `note`, `frame`, `repeat`, `side`) with `source`, `summary` and
  `side_into`, for models that are more than one transformer stack. Schema, template and
  conventions ship together.
- **Added** these fields to records whose source was fetched and read: the encoder-decoder text
  models (BART, T5, NLLB, TAPEX), Whisper, the audio encoders (AST, wav2vec2), the Qwen3
  rerankers, and `embedding_norm` on the BERT-family encoders.
- **Added** to `fingerprint`: the `norm_placement` value `both` (a norm before and after each
  sub-layer), `parallel_sublayers` (`shared-norm`, `separate-norms`) and `output_head`
  (`cell-selection`, `cell-selection-aggregation`). **Added** the `pipeline` stage roles
  `textEncoder`, `denoiser`, `autoencoder` and `step`.
- **Added** from fetched sources: the parallel layout of Phi-2, Falcon-7B and the three Pythia
  models; TAPAS (encoder layout and head); the jina reranker (from its own block code); Gemma 2
  2B, 9B and 27B (`both`; the gated 2B and 27B take their numbers from Table 1 of the Gemma 2
  paper); Llama 3 8B and Llama 3.1 70B (from Table 3 of "The Llama 3 Herd of Models").
- **Added** `fingerprint.layer_mixers` (`names[]`, `pattern`, `note`, `feed_forward`) for models
  whose layers do not all hold the same sequence mixer, counted from each model's configuration
  and read from its code: the three Nemotron 3 records (Mamba-2, MoE and attention layers; their
  `normalization` corrected to RMSNorm from the code), Qwen3-Coder-Next and Qwen3.8-27B (three
  Gated DeltaNet layers, then one attention layer; Qwen3.8-27B also gets a `pipeline`).
- **Added** to `pipeline`: `into` on a side stage (a further side input with its own target),
  `branch` with `branch_from` (a second output), and `inner_frame` with `inner_repeat` (a
  repeated block inside a loop). FLUX.1-dev, Qwen2.5-Omni and MiniMax H3 are rearranged to use
  them, from the notes their stages already carried; EmbeddingGemma 2, Qwen3.8-Flash-Next and
  Kimi K3 get a `pipeline` from their own code and configuration.
- **Fixed** from the sources: Nemotron 3.5 Transcribe is a mixture-of-experts model with 4.3B
  active parameters (its card); DeepSeek-OCR and Unlimited-OCR use multi-head attention, not MLA
  (`use_mla: false` in their configuration).

### Schema 2.6: the `quantization-topic` type (additive)

- **Added** the `quantization-topic` type (folder `quantization/`): one page of the Quantization
  guide, with `order`, `summary`, computed figures in `visuals` (`bit_layout`, `affine_example`,
  `model_memory`, `name_decoder`) and sourced `tables` (`section`, `title`, `columns`,
  `rows[]` of `cells`, `source`, the shape of `key_numbers`). Body sections: What It Is, the
  page's own topics, then the optional What To Watch. Schema, registry entry, `meta.json` type,
  the base `type` enum and the template ship together.
- **Added** two pilot pages, every fact from a source fetched for them: `number-formats` (how a
  weight is stored as FP32, FP16, BF16, INT8 and 4-bit; affine quantization worked by hand;
  memory per precision for Llama 3.1 8B Instruct; the perplexity increase llama.cpp lists per
  file type) and `reading-quantized-file-names` (GGUF and its naming convention; what Q, the
  number, `_0`, `_K`, `_S`/`_M`/`_L` and `IQ` mean; bits per weight per llama.cpp type from
  `ggml-common.h` and per file from the llama.cpp quantize README; bitsandbytes, GPTQ, AWQ, EXL2,
  MLX and Unsloth Dynamic files).

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
