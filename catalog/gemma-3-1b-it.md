---
id: model/gemma-3-1b-it
type: model
slug: gemma-3-1b-it
schema_version: 3.0
title: gemma-3-1b-it
organization: organization/google-deepmind
release_date: '2025-03-10'
license: license/gemma
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
total_parameters: 999885952
base_models:
- name: google/gemma-3-1b-pt
  address: https://huggingface.co/google/gemma-3-1b-pt
summary: Gemma 3 1B IT is the instruction-tuned 1-billion-parameter size of Gemma 3, a family of open models from Google DeepMind. Instruction-tuned means the pre-trained model, google/gemma-3-1b-pt, was trained further to follow instructions and hold a conversation. The family card lists support for over 140 languages and gives the 1B size a 32K-token input and output context. The Hugging Face card is gated, so this text follows Google's own Gemma 3 model card.
intended_use: Google's card lists uses its creators considered, and says the list is not comprehensive. They include text generation (poems, scripts, code, marketing copy, email drafts), chatbots, text summarization and image data extraction, plus research and education uses such as language learning tools and exploring large bodies of text. The card points to the Gemma Prohibited Use Policy for uses that are not allowed.
what_sets_it_apart: The card gives size-specific facts. The 1B size was trained on 2 trillion tokens of text, against 4, 12 and 14 trillion for the 4B, 12B and 27B sizes, and its context is 32K tokens against 128K for those larger sizes. The training data has a knowledge cutoff of August 2024 and includes web documents, code and content in over 140 languages.
limitations: The card says the models may generate incorrect or outdated facts because they are not knowledge bases. It adds that they work better on tasks with clear prompts than on open-ended or highly complex ones, may miss nuance, sarcasm or figurative language, may lack common sense, and reflect gaps and biases in their training data.
filters:
  size_category: tiny
  is_gated: true
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 197069
  likes: 1326
  last_modified: '2025-04-04'
  observed_at: '2026-11-20'
  source: https://huggingface.co/google/gemma-3-1b-it
aliases:
- google/gemma-3-1b-it
lineage:
  parents: []
  children: []
quotes:
  summary: Total input context of 128K tokens for the 4B, 12B, and 27B sizes, and 32K tokens for the 1B and 270M sizes.
  intended_use: 'Text Summarization: Generate concise summaries of a text corpus, research papers, or reports.'
  what_sets_it_apart: the 1B with 2 trillion tokens, and the 270M with 6 trillion tokens.
  limitations: They may generate incorrect or outdated factual statements.
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/gemma-3-1b-it
  title: google/gemma-3-1b-it — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/1905.07830
  title: arXiv:1905.07830
  trust: 0.95
- url: https://arxiv.org/abs/1905.10044
  title: arXiv:1905.10044
  trust: 0.95
- url: https://arxiv.org/abs/1911.11641
  title: arXiv:1911.11641
  trust: 0.95
- url: https://arxiv.org/abs/1904.09728
  title: arXiv:1904.09728
  trust: 0.95
- url: https://arxiv.org/abs/1705.03551
  title: arXiv:1705.03551
  trust: 0.95
- url: https://arxiv.org/abs/1911.01547
  title: arXiv:1911.01547
  trust: 0.95
- url: https://arxiv.org/abs/1907.10641
  title: arXiv:1907.10641
  trust: 0.95
- url: https://arxiv.org/abs/1903.00161
  title: arXiv:1903.00161
  trust: 0.95
- url: https://arxiv.org/abs/2009.03300
  title: arXiv:2009.03300
  trust: 0.95
- url: https://arxiv.org/abs/2304.06364
  title: arXiv:2304.06364
  trust: 0.95
- url: https://arxiv.org/abs/2103.03874
  title: arXiv:2103.03874
  trust: 0.95
- url: https://arxiv.org/abs/2110.14168
  title: arXiv:2110.14168
  trust: 0.95
- url: https://arxiv.org/abs/2311.12022
  title: arXiv:2311.12022
  trust: 0.95
- url: https://arxiv.org/abs/2108.07732
  title: arXiv:2108.07732
  trust: 0.95
- url: https://arxiv.org/abs/2107.03374
  title: arXiv:2107.03374
  trust: 0.95
- url: https://arxiv.org/abs/2210.03057
  title: arXiv:2210.03057
  trust: 0.95
- url: https://arxiv.org/abs/2106.03193
  title: arXiv:2106.03193
  trust: 0.95
- url: https://arxiv.org/abs/1910.11856
  title: arXiv:1910.11856
  trust: 0.95
- url: https://arxiv.org/abs/2502.12404
  title: arXiv:2502.12404
  trust: 0.95
- url: https://arxiv.org/abs/2502.21228
  title: arXiv:2502.21228
  trust: 0.95
- url: https://arxiv.org/abs/2404.16816
  title: arXiv:2404.16816
  trust: 0.95
- url: https://arxiv.org/abs/2104.12756
  title: arXiv:2104.12756
  trust: 0.95
- url: https://arxiv.org/abs/2311.16502
  title: arXiv:2311.16502
  trust: 0.95
- url: https://arxiv.org/abs/2203.10244
  title: arXiv:2203.10244
  trust: 0.95
- url: https://arxiv.org/abs/2404.12390
  title: arXiv:2404.12390
  trust: 0.95
- url: https://arxiv.org/abs/1810.12440
  title: arXiv:1810.12440
  trust: 0.95
- url: https://arxiv.org/abs/1908.02660
  title: arXiv:1908.02660
  trust: 0.95
- url: https://arxiv.org/abs/2312.11805
  title: arXiv:2312.11805
  trust: 0.95
- url: https://ai.google.dev/gemma/docs/core/model_card_3
  title: Gemma 3 model card (Google AI for Developers)
  trust: 0.95
---


