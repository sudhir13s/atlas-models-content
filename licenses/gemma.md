---
id: license/gemma
type: license
slug: gemma
schema_version: 3.1
title: Gemma Terms of Use
name: Gemma Terms of Use
spdx_id: Gemma
category: custom
url: https://ai.google.dev/gemma/terms
permissions:
  commercial_use: true
  modification: true
  distribution: true
  fine_tuning: true
  output_training: true
  patent_grant: false
  private_use: true
conditions:
- Include the Gemma Terms of Use with any distribution of the model or derivatives
- Pass the same use restrictions through to downstream recipients
- Provide notice that you modified the files
- Do not remove or alter the use-restriction terms
limitations:
- Prohibited Use Policy restrictions apply to all use and outputs
- No trademark rights to the 'Gemma' or Google marks beyond descriptive use
- No warranty
- No liability
summary: 'Custom Google license: free commercial use, modification, fine-tuning and distribution, but bound by a Prohibited Use Policy that must flow through to every downstream user.'
tags:
- license
- custom
- gemma
- google
updated: '2026-07-17'
sources:
- url: https://ai.google.dev/gemma/terms
  title: Gemma Terms of Use
  trust: 0.95
- url: https://ai.google.dev/gemma/prohibited_use_policy
  title: Gemma Prohibited Use Policy
  trust: 0.95
---

## Plain-English

The Gemma Terms of Use are a custom Google license, not a standard open-source one, though in practice
they are quite permissive. You may use Gemma commercially, modify it, fine-tune it, distribute it and
its derivatives, and generate and use outputs freely — there is **no user-count threshold** like the
Llama or Qwen licenses impose. The binding condition is Google's **Prohibited Use Policy**: certain
uses (for example, generating harmful, deceptive, or illegal content) are forbidden, and whenever you
distribute Gemma or a model derived from it you must pass these same use restrictions on to your
recipients and include the Gemma terms. You must also flag that you changed the files if you modified
them. There is no patent grant, no warranty, and no trademark license beyond referring to the model by
name. Treat it as "open weight, commercially friendly, with an enforceable acceptable-use policy that
travels with the model."
