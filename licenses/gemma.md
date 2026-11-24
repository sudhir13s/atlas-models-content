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
- Give every recipient of Gemma or a model derivative a copy of the agreement
- Include the use restrictions as an enforceable provision in your terms and tell downstream users they apply
- Mark modified files with a prominent notice of the change
- Ship a Notice text file pointing to the Gemma Terms of Use with every distribution other than a hosted service
- Any additional terms you impose must not conflict with the agreement
limitations:
- The Prohibited Use Policy and applicable laws restrict all use of Gemma and model derivatives
- Google may restrict use it reasonably believes violates the agreement, remotely or otherwise
- No rights to use Google's trademarks, trade names or logos
- Google may terminate the agreement on breach, and you must then delete all copies
- No warranty
- No liability
summary: 'Custom Google license: commercial use, modification, fine-tuning and distribution are allowed, but bound by a Prohibited Use Policy that must flow through to every downstream user.'
tags:
- license
- custom
- gemma
- google
updated: '2026-11-24'
sources:
- url: https://ai.google.dev/gemma/terms
  title: Gemma Terms of Use
  trust: 0.95
- url: https://ai.google.dev/gemma/prohibited_use_policy
  title: Gemma Prohibited Use Policy
  trust: 0.95
---

## Plain-English

The Gemma Terms of Use are a custom Google license, not a standard open-source one. They cover the
models listed in their appendix, from Gemma 1 to Gemma 3n and variants; Gemma 4 has its own license.
You may use Gemma commercially, modify it, fine-tune it, distribute it and its derivatives, and
Google claims no rights in the outputs you generate — there is **no user-count threshold** like the
Llama or Qwen licenses impose. The binding condition is Google's **Prohibited Use Policy**: certain
uses (for example, dangerous, illegal, malicious, deceptive or fraudulent activities) are forbidden,
and whenever you distribute Gemma or a model derived from it you must pass these same use
restrictions on to your recipients and include the Gemma terms. You must also flag that you changed
the files if you modified them. There is no patent grant, no warranty, and no right to use Google's
trademarks. Treat it as "open weight, commercially friendly, with an enforceable acceptable-use
policy that travels with the model."
