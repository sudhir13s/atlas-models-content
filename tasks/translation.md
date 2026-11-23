---
id: task/translation
type: task
slug: translation
schema_version: 3.1
title: Translation
group: task-group/language
order: 13
summary: Convert text from one human language into another while keeping its meaning.
hf_pipeline_tags:
- translation
inputs:
- text
outputs:
- text
benchmarks:
- benchmark/wmt24pp
tags:
- translation
- multilingual
- sequence-to-sequence
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/translation
  title: Hugging Face — Translation task
  trust: 0.9
---

## What It Is

A translation model takes text in a source language and **rewrites it in a target language**.

- **Single-pair models:** trained for one direction, such as English to French; small and fast.
- **Multilingual models:** one model covers many languages, selected by a language code in the request.
- **Encoder-decoder design:** most dedicated models read the whole source sentence, then write the translation.
- **Sentence focus:** many translate one sentence at a time and do not see the surrounding document.

## When To Use

- **Use it** for large volumes of text in known language pairs, or where translation must run offline.
- **Use a chat model instead** when tone, terminology or document-wide consistency must follow instructions.
- **Use text classification first** to detect the source language when it is unknown.
- **Use summarization as well** when the reader needs a short version, not the full text.

## How To Choose

- **Language pair and direction:** quality differs for each pair and is often lower for less common languages.
- **Domain:** general models mistranslate legal, medical and technical terms.
- **Context handled:** sentence-level or document-level, which affects pronouns and consistent terms.
- **Speed and size:** words per second on your hardware against the quality you need.
