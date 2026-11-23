---
id: model/kimi-k2-5
type: model
slug: kimi-k2-5
schema_version: 3.0
title: Kimi-K2.5
organization: organization/moonshot-ai
release_date: '2026-01-01'
license_note: modified-mit
model_category: multimodal
model_kind: multimodal
tasks:
- task/vision-language-models
total_parameters: 1026879376368
context_window_tokens: 262144
summary: Kimi K2.5 is an open-source model from moonshotai that takes both images and text and is built for agent work, where a model calls tools and carries out multi-step tasks. The card says it was built by continued pretraining on about 15 trillion mixed visual and text tokens on top of Kimi-K2-Base. It lists a Mixture-of-Experts design with 1T total parameters, 32B activated per token, and a 256K context length.
intended_use: 'The card describes instant and thinking modes and both conversational and agentic use. One highlighted use is coding with vision: generating code from visual specifications such as UI designs and video workflows, and orchestrating tools for visual data processing. The card says chat with video content is experimental and available only through the official API.'
what_sets_it_apart: The card describes native multimodality, meaning the model was pretrained on vision and language tokens together rather than having vision added afterward. It also describes an Agent Swarm scheme, in which the model splits a complex task into parallel sub-tasks run by agents it creates on the fly, each specialized for its domain. Its weights use native INT4 quantization, the same method as Kimi-K2-Thinking.
running_it: For third-party serving with vLLM or SGLang, the card recommends a temperature of 1.0 for Thinking mode and 0.6 for Instant mode, with top_p of 0.95.
fingerprint:
  attention_type: MLA
  num_layers: 61
  attention_heads: 64
  kv_heads: 64
  head_size: 112
  hidden_size: 7168
  intermediate_size: 18432
  vocabulary_size: 163840
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 50000.0
moe_config:
  expert_count: 384
  active_experts: 8
  shared_expert: true
pipeline:
  source: https://huggingface.co/moonshotai/Kimi-K2.5/blob/main/modeling_kimi_k25.py
  summary: A vision encoder (left) turns an image or video into vectors, neighbouring patches are merged, and a projector maps them to the language model's width. They take their places among the text token embeddings, and a 61-layer mixture-of-experts language model reads the whole sequence and writes text.
  side_into: merge
  stages:
  - id: media
    role: input
    title: Image or video
    side: true
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 14 × 14 pixel patches · width 1152
    note: A convolution turns each 14 by 14 pixel patch into one vector, and learned position embeddings for height, width and time are added.
    side: true
  - id: vision-block
    role: visionEncoder
    title: Attention + feed-forward
    details:
    - 16 heads · width 1152
    note: For video, attention runs across space and time.
    frame: Vision block
    repeat: 27
    side: true
  - id: patch-merge
    role: step
    title: Merge 2 × 2 patches
    details:
    - video frames averaged over time
    note: Each 2 by 2 group of neighbouring patch vectors is gathered into one position; for video, the frames are averaged.
    side: true
  - id: projector
    role: projector
    title: Projector
    details:
    - LayerNorm · MLP 4608 → 7168
    note: The four gathered vectors are normalized and joined into one of width 4608, and a two-layer network with GELU maps it to the language model's width.
    side: true
  - id: text
    role: tokenizer
    title: Text tokens
    details:
    - vocabulary 163,840
  - id: embedding
    role: embedding
    title: Token embedding
    details:
    - width 7168
  - id: merge
    role: embedding
    title: Place among text embeddings
    details:
    - at the media placeholder tokens
    note: The image or video vectors take the places the prompt reserves with the media placeholder token, so text and vision form one sequence.
  - id: dense-layer
    role: attention
    title: Attention + feed-forward
    details:
    - dense feed-forward
    note: The first layer has the same latent attention as the others but one ordinary feed-forward network instead of experts.
    frame: First layer
  - id: attention
    role: attention
    title: Multi-head latent attention
    details:
    - 64 heads · keys and values via 512
    note: Keys and values are computed from a compressed vector of width 512 (and queries from one of width 1536), which keeps the cache small.
    frame: MoE block
    repeat: 60
  - id: router
    role: router
    title: Router
    details:
    - sigmoid scores · top 8 of 384
    note: Scores every routed expert for each token and keeps the eight highest.
    frame: MoE block
  - id: experts
    role: experts
    title: Experts
    details:
    - 8 routed + 1 shared
    note: The eight chosen experts and one shared expert that every token uses each run a small feed-forward network; their outputs are added.
    frame: MoE block
  - id: norm
    role: normalisation
    title: Final RMSNorm
  - id: output
    role: outputHead
    title: Output head
    details:
    - logits over 163,840
filters:
  size_category: xlarge
  is_quantized: false
  is_moe: true
  is_gated: false
  pipeline_tag: image-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 392853
  likes: 2902
  last_modified: '2026-11-20'
  observed_at: '2026-11-20'
  source: https://huggingface.co/moonshotai/Kimi-K2.5
variants:
- name: nvidia/Kimi-K2.5-NVFP4
  format: nvfp4
  address: https://huggingface.co/nvidia/Kimi-K2.5-NVFP4
  publisher: nvidia
aliases:
- moonshotai/Kimi-K2.5
- kimi-k2.5-nvfp4
lineage:
  parents: []
  children: []
quotes:
  summary: built through continual pretraining on approximately 15 trillion mixed visual and text tokens atop Kimi-K2-Base
  intended_use: K2.5 generates code from visual specifications (UI designs, video workflows)
  what_sets_it_apart: It decomposes complex tasks into parallel sub-tasks executed by dynamically instantiated, domain-specific agents.
  running_it: The recommended `temperature` will be `1.0` for Thinking mode and `0.6` for Instant mode.
tags:
- llm
- kimi-k25
updated: '2026-11-23'
sources:
- url: https://huggingface.co/moonshotai/Kimi-K2.5
  title: moonshotai/Kimi-K2.5 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/moonshotai/Kimi-K2.5/blob/main/config.json
  title: moonshotai/Kimi-K2.5 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2602.02276
  title: arXiv:2602.02276
  trust: 0.95
- url: https://huggingface.co/moonshotai/Kimi-K2.5/blob/main/modeling_kimi_k25.py
  title: moonshotai/Kimi-K2.5 — modeling_kimi_k25.py (Moonshot AI)
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/deepseek_v3/modeling_deepseek_v3.py
  title: 'transformers/models/deepseek_v3/modeling_deepseek_v3.py: language model implementation (Hugging Face Transformers)'
  trust: 0.9
---


