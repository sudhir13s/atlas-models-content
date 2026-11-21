---
id: architecture-era/bert
type: architecture-era
slug: bert
schema_version: 1.0
title: "BERT (Bidirectional Pretraining)"
name: "BERT"
era: scaling
era_range: "2018–2020"
year: 2018
family: llm
core_innovation: "Encoder-only bidirectional pretraining via masked language modeling, producing reusable contextual representations for fine-tuning."
description: "The encoder-only transformer whose masked-LM pretraining set the pretrain-then-fine-tune paradigm for NLP."
key_features: ["Encoder-only transformer", "Masked language modeling", "Next-sentence prediction", "Bidirectional context", "Fine-tune per downstream task"]
key_models: ["BERT-base/large (2018)", "RoBERTa (2019)", "DeBERTa (2020)"]
paper_title: "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding"
paper_url: "https://arxiv.org/abs/1810.04805"
influenced_by: [architecture-era/transformer]
led_to: []

eli5: "BERT learns language by playing fill-in-the-blank. Hide a few words in a sentence and make the model guess them using the words on both sides. Do that billions of times and it builds a deep sense of what words mean in context — which you can then reuse for almost any language task."

analogy:
  title: "Cloze-test study, both sides visible"
  body: "Think of the fill-in-the-blank exercises from school: 'The ___ chased the ball into the street.' To answer, you read what comes before AND after the blank — 'chased', 'ball', 'street' all hint at 'dog'. BERT studies by turning huge amounts of text into millions of these blanks and getting graded on its guesses. A left-to-right model only sees the words before the blank, like reading with a card covering the rest of the line; BERT lifts the card and reads both sides. That two-sided reading is what makes its understanding deep."

narrative:
  motivation: "The transformer was a powerful architecture, but the language models built on it still read text left-to-right, so a word's representation could only depend on what came before it. For understanding tasks — is this review positive? does sentence A entail sentence B? where's the answer in this passage? — that's a real handicap, because meaning depends on the whole context, not just the left half."
  approach: "Keep only the transformer's encoder (the part designed to build representations, not generate text) and change the training game. Instead of predicting the next word, randomly hide about 15% of the tokens and train the model to reconstruct them from everything around them — a masked language model (MLM). Add a secondary next-sentence-prediction task to teach sentence-pair relationships."
  solution: "Masking is the key trick. Because the model never sees the answer and must recover it from both directions at once, each token's encoding is shaped by its full left-and-right context — genuinely bidirectional. Pretrain this once on a huge unlabeled corpus (Wikipedia + BookCorpus), then attach a small task-specific head and fine-tune on each downstream dataset, which is cheap by comparison."
  outcome: "BERT swept the language-understanding benchmarks of its day (GLUE, SQuAD) and made 'download a pretrained encoder, fine-tune a light head' the default NLP workflow. It became the understanding-first, encoder-only complement to the generation-first, decoder-only GPT line — two branches off the same transformer trunk. Follow-ups (RoBERTa, DeBERTa) showed the pretraining recipe mattered even more than the extra objectives."

build_steps:
  - { title: "1. Tokenize and add special tokens", detail: "Split text into WordPiece subword tokens and prepend a [CLS] token (its final vector summarizes the whole input) with [SEP] separating sentence pairs." }
  - { title: "2. Embed tokens, positions, and segments", detail: "Each token gets a learned embedding plus a positional embedding (order) plus a segment embedding (sentence A vs B). These three are summed into the input vectors." }
  - { title: "3. Mask about 15% of tokens", detail: "Randomly replace chosen tokens with a [MASK] placeholder (most of the time) so the model has something to reconstruct — this is the fill-in-the-blank signal." }
  - { title: "4. Encode with a bidirectional stack", detail: "Run the sequence through N transformer-encoder blocks. With no causal mask, every token attends to every other token, left and right — the source of bidirectional context." }
  - { title: "5. Predict the masked words", detail: "At each masked position, project the final vector over the vocabulary and train with cross-entropy against the true token. A next-sentence-prediction head trains the [CLS] vector too." }
  - { title: "6. Fine-tune per task", detail: "Throw away the pretraining heads, attach a small task head (classification on [CLS], span prediction on token outputs), and fine-tune the whole model briefly on labeled data." }

key_equation:
  latex: "\\mathcal{L}_{\\mathrm{MLM}}=-\\sum_{i\\in M}\\log P\\!\\left(x_i \\mid x_{\\setminus M}\\right)"
  plain: "For every masked position, the model outputs a probability for the true word given all the unmasked words around it. The loss is the sum of the negative log-probabilities of those true words — so training pushes the model to make the correct blank-filling as likely as possible, using context from both sides."
  terms:
    - { symbol: "\\mathcal{L}_{\\mathrm{MLM}}", meaning: "the masked-language-model training loss to minimize" }
    - { symbol: "M", meaning: "the set of positions that were masked out (~15% of tokens)" }
    - { symbol: "x_i", meaning: "the true token that was hidden at masked position i" }
    - { symbol: "x_{\\setminus M}", meaning: "all the visible (unmasked) tokens — the two-sided context" }
    - { symbol: "P(x_i \\mid x_{\\setminus M})", meaning: "the model's predicted probability of the correct token given that context" }

strengths:
  - "Deeply bidirectional — each token is encoded using both left and right context, ideal for understanding tasks."
  - "One expensive pretraining amortizes across many cheap fine-tunes, transferring general language knowledge widely."
  - "The [CLS] and per-token outputs plug into lightweight heads for classification, tagging, and span extraction."
  - "Self-supervised: learns from raw unlabeled text, so pretraining data is essentially unlimited."

weaknesses:
  - "Not a generator — the bidirectional, masked objective makes BERT poor at free-form text generation."
  - "The [MASK] token appears only in pretraining, not at inference, creating a train/test mismatch the model must paper over."
  - "Only ~15% of tokens contribute loss per example, so pretraining is sample-inefficient versus next-token prediction."
  - "Next-sentence prediction turned out to add little; RoBERTa dropped it and improved by simply training longer on more data."

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 880 545" role="img" aria-label="BERT pre-training: each input token of [CLS] sentence A [SEP] sentence B [SEP] is the sum of a token, a segment and a position embedding; a stack of bidirectional encoder blocks lets every token attend to every other token; a masked-language-model head predicts the hidden word at the [MASK] position and a next-sentence head classifies the [CLS] output.">
  <defs><marker id="bert-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="bert-arrow-strong" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="bert-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <text class="d-label" x="20" y="498" text-anchor="start">Token</text>
  <text class="d-label" x="20" y="470" text-anchor="start">+ Segment</text>
  <text class="d-label" x="20" y="442" text-anchor="start">+ Position</text>
  <text class="d-label" x="20" y="404" text-anchor="start">= input</text>
  <text class="d-label" x="150" y="530" text-anchor="middle">[CLS]</text>
  <rect class="d-box d-role-embedding" x="122" y="484" width="56" height="20" rx="3"/>
  <text class="d-note" x="150" y="497" text-anchor="middle">E<tspan class="d-note" dy="3">[CLS]</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box" x="122" y="456" width="56" height="20" rx="3"/>
  <text class="d-note" x="150" y="469" text-anchor="middle">E<tspan class="d-note" dy="3">A</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box d-role-position" x="122" y="428" width="56" height="20" rx="3"/>
  <text class="d-note" x="150" y="441" text-anchor="middle">E<tspan class="d-note" dy="3">0</tspan><tspan dy="-3"> </tspan></text>
  <path class="d-line-strong" d="M150 428 V414" marker-end="url(#bert-arrow-strong)"/>
  <text class="d-label" x="218" y="530" text-anchor="middle">my</text>
  <rect class="d-box d-role-embedding" x="190" y="484" width="56" height="20" rx="3"/>
  <text class="d-note" x="218" y="497" text-anchor="middle">E<tspan class="d-note" dy="3">my</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box" x="190" y="456" width="56" height="20" rx="3"/>
  <text class="d-note" x="218" y="469" text-anchor="middle">E<tspan class="d-note" dy="3">A</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box d-role-position" x="190" y="428" width="56" height="20" rx="3"/>
  <text class="d-note" x="218" y="441" text-anchor="middle">E<tspan class="d-note" dy="3">1</tspan><tspan dy="-3"> </tspan></text>
  <path class="d-line-strong" d="M218 428 V414" marker-end="url(#bert-arrow-strong)"/>
  <text class="d-label" x="286" y="530" text-anchor="middle">dog</text>
  <rect class="d-box d-role-embedding" x="258" y="484" width="56" height="20" rx="3"/>
  <text class="d-note" x="286" y="497" text-anchor="middle">E<tspan class="d-note" dy="3">dog</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box" x="258" y="456" width="56" height="20" rx="3"/>
  <text class="d-note" x="286" y="469" text-anchor="middle">E<tspan class="d-note" dy="3">A</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box d-role-position" x="258" y="428" width="56" height="20" rx="3"/>
  <text class="d-note" x="286" y="441" text-anchor="middle">E<tspan class="d-note" dy="3">2</tspan><tspan dy="-3"> </tspan></text>
  <path class="d-line-strong" d="M286 428 V414" marker-end="url(#bert-arrow-strong)"/>
  <text class="d-label" x="354" y="530" text-anchor="middle">[MASK]</text>
  <rect class="d-box d-role-embedding" x="326" y="484" width="56" height="20" rx="3"/>
  <text class="d-note" x="354" y="497" text-anchor="middle">E<tspan class="d-note" dy="3">[MASK]</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box" x="326" y="456" width="56" height="20" rx="3"/>
  <text class="d-note" x="354" y="469" text-anchor="middle">E<tspan class="d-note" dy="3">A</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box d-role-position" x="326" y="428" width="56" height="20" rx="3"/>
  <text class="d-note" x="354" y="441" text-anchor="middle">E<tspan class="d-note" dy="3">3</tspan><tspan dy="-3"> </tspan></text>
  <path class="d-line-strong" d="M354 428 V414" marker-end="url(#bert-arrow-strong)"/>
  <text class="d-label" x="422" y="530" text-anchor="middle">cute</text>
  <rect class="d-box d-role-embedding" x="394" y="484" width="56" height="20" rx="3"/>
  <text class="d-note" x="422" y="497" text-anchor="middle">E<tspan class="d-note" dy="3">cute</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box" x="394" y="456" width="56" height="20" rx="3"/>
  <text class="d-note" x="422" y="469" text-anchor="middle">E<tspan class="d-note" dy="3">A</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box d-role-position" x="394" y="428" width="56" height="20" rx="3"/>
  <text class="d-note" x="422" y="441" text-anchor="middle">E<tspan class="d-note" dy="3">4</tspan><tspan dy="-3"> </tspan></text>
  <path class="d-line-strong" d="M422 428 V414" marker-end="url(#bert-arrow-strong)"/>
  <text class="d-label" x="490" y="530" text-anchor="middle">[SEP]</text>
  <rect class="d-box d-role-embedding" x="462" y="484" width="56" height="20" rx="3"/>
  <text class="d-note" x="490" y="497" text-anchor="middle">E<tspan class="d-note" dy="3">[SEP]</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box" x="462" y="456" width="56" height="20" rx="3"/>
  <text class="d-note" x="490" y="469" text-anchor="middle">E<tspan class="d-note" dy="3">A</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box d-role-position" x="462" y="428" width="56" height="20" rx="3"/>
  <text class="d-note" x="490" y="441" text-anchor="middle">E<tspan class="d-note" dy="3">5</tspan><tspan dy="-3"> </tspan></text>
  <path class="d-line-strong" d="M490 428 V414" marker-end="url(#bert-arrow-strong)"/>
  <text class="d-label" x="558" y="530" text-anchor="middle">he</text>
  <rect class="d-box d-role-embedding" x="530" y="484" width="56" height="20" rx="3"/>
  <text class="d-note" x="558" y="497" text-anchor="middle">E<tspan class="d-note" dy="3">he</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box" x="530" y="456" width="56" height="20" rx="3"/>
  <text class="d-note" x="558" y="469" text-anchor="middle">E<tspan class="d-note" dy="3">B</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box d-role-position" x="530" y="428" width="56" height="20" rx="3"/>
  <text class="d-note" x="558" y="441" text-anchor="middle">E<tspan class="d-note" dy="3">6</tspan><tspan dy="-3"> </tspan></text>
  <path class="d-line-strong" d="M558 428 V414" marker-end="url(#bert-arrow-strong)"/>
  <text class="d-label" x="626" y="530" text-anchor="middle">likes</text>
  <rect class="d-box d-role-embedding" x="598" y="484" width="56" height="20" rx="3"/>
  <text class="d-note" x="626" y="497" text-anchor="middle">E<tspan class="d-note" dy="3">likes</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box" x="598" y="456" width="56" height="20" rx="3"/>
  <text class="d-note" x="626" y="469" text-anchor="middle">E<tspan class="d-note" dy="3">B</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box d-role-position" x="598" y="428" width="56" height="20" rx="3"/>
  <text class="d-note" x="626" y="441" text-anchor="middle">E<tspan class="d-note" dy="3">7</tspan><tspan dy="-3"> </tspan></text>
  <path class="d-line-strong" d="M626 428 V414" marker-end="url(#bert-arrow-strong)"/>
  <text class="d-label" x="694" y="530" text-anchor="middle">play</text>
  <rect class="d-box d-role-embedding" x="666" y="484" width="56" height="20" rx="3"/>
  <text class="d-note" x="694" y="497" text-anchor="middle">E<tspan class="d-note" dy="3">play</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box" x="666" y="456" width="56" height="20" rx="3"/>
  <text class="d-note" x="694" y="469" text-anchor="middle">E<tspan class="d-note" dy="3">B</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box d-role-position" x="666" y="428" width="56" height="20" rx="3"/>
  <text class="d-note" x="694" y="441" text-anchor="middle">E<tspan class="d-note" dy="3">8</tspan><tspan dy="-3"> </tspan></text>
  <path class="d-line-strong" d="M694 428 V414" marker-end="url(#bert-arrow-strong)"/>
  <text class="d-label" x="762" y="530" text-anchor="middle">[SEP]</text>
  <rect class="d-box d-role-embedding" x="734" y="484" width="56" height="20" rx="3"/>
  <text class="d-note" x="762" y="497" text-anchor="middle">E<tspan class="d-note" dy="3">[SEP]</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box" x="734" y="456" width="56" height="20" rx="3"/>
  <text class="d-note" x="762" y="469" text-anchor="middle">E<tspan class="d-note" dy="3">B</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box d-role-position" x="734" y="428" width="56" height="20" rx="3"/>
  <text class="d-note" x="762" y="441" text-anchor="middle">E<tspan class="d-note" dy="3">9</tspan><tspan dy="-3"> </tspan></text>
  <path class="d-line-strong" d="M762 428 V414" marker-end="url(#bert-arrow-strong)"/>
  <text class="d-label" x="20" y="352.5" text-anchor="start">Block 1</text>
  <text class="d-note" x="20" y="366.5" text-anchor="start">all-to-all</text>
  <path class="d-line" d="M150 388 L150 317" opacity="0.45"/>
  <path class="d-line" d="M150 388 L218 317" opacity="0.45"/>
  <path class="d-line" d="M150 388 L286 317" opacity="0.45"/>
  <path class="d-line" d="M150 388 L422 317" opacity="0.45"/>
  <path class="d-line" d="M150 388 L490 317" opacity="0.45"/>
  <path class="d-line" d="M150 388 L558 317" opacity="0.45"/>
  <path class="d-line" d="M150 388 L626 317" opacity="0.45"/>
  <path class="d-line" d="M150 388 L694 317" opacity="0.45"/>
  <path class="d-line" d="M150 388 L762 317" opacity="0.45"/>
  <path class="d-line" d="M218 388 L150 317" opacity="0.45"/>
  <path class="d-line" d="M218 388 L218 317" opacity="0.45"/>
  <path class="d-line" d="M218 388 L286 317" opacity="0.45"/>
  <path class="d-line" d="M218 388 L422 317" opacity="0.45"/>
  <path class="d-line" d="M218 388 L490 317" opacity="0.45"/>
  <path class="d-line" d="M218 388 L558 317" opacity="0.45"/>
  <path class="d-line" d="M218 388 L626 317" opacity="0.45"/>
  <path class="d-line" d="M218 388 L694 317" opacity="0.45"/>
  <path class="d-line" d="M218 388 L762 317" opacity="0.45"/>
  <path class="d-line" d="M286 388 L150 317" opacity="0.45"/>
  <path class="d-line" d="M286 388 L218 317" opacity="0.45"/>
  <path class="d-line" d="M286 388 L286 317" opacity="0.45"/>
  <path class="d-line" d="M286 388 L422 317" opacity="0.45"/>
  <path class="d-line" d="M286 388 L490 317" opacity="0.45"/>
  <path class="d-line" d="M286 388 L558 317" opacity="0.45"/>
  <path class="d-line" d="M286 388 L626 317" opacity="0.45"/>
  <path class="d-line" d="M286 388 L694 317" opacity="0.45"/>
  <path class="d-line" d="M286 388 L762 317" opacity="0.45"/>
  <path class="d-line" d="M354 388 L150 317" opacity="0.45"/>
  <path class="d-line" d="M354 388 L218 317" opacity="0.45"/>
  <path class="d-line" d="M354 388 L286 317" opacity="0.45"/>
  <path class="d-line" d="M354 388 L422 317" opacity="0.45"/>
  <path class="d-line" d="M354 388 L490 317" opacity="0.45"/>
  <path class="d-line" d="M354 388 L558 317" opacity="0.45"/>
  <path class="d-line" d="M354 388 L626 317" opacity="0.45"/>
  <path class="d-line" d="M354 388 L694 317" opacity="0.45"/>
  <path class="d-line" d="M354 388 L762 317" opacity="0.45"/>
  <path class="d-line" d="M422 388 L150 317" opacity="0.45"/>
  <path class="d-line" d="M422 388 L218 317" opacity="0.45"/>
  <path class="d-line" d="M422 388 L286 317" opacity="0.45"/>
  <path class="d-line" d="M422 388 L422 317" opacity="0.45"/>
  <path class="d-line" d="M422 388 L490 317" opacity="0.45"/>
  <path class="d-line" d="M422 388 L558 317" opacity="0.45"/>
  <path class="d-line" d="M422 388 L626 317" opacity="0.45"/>
  <path class="d-line" d="M422 388 L694 317" opacity="0.45"/>
  <path class="d-line" d="M422 388 L762 317" opacity="0.45"/>
  <path class="d-line" d="M490 388 L150 317" opacity="0.45"/>
  <path class="d-line" d="M490 388 L218 317" opacity="0.45"/>
  <path class="d-line" d="M490 388 L286 317" opacity="0.45"/>
  <path class="d-line" d="M490 388 L422 317" opacity="0.45"/>
  <path class="d-line" d="M490 388 L490 317" opacity="0.45"/>
  <path class="d-line" d="M490 388 L558 317" opacity="0.45"/>
  <path class="d-line" d="M490 388 L626 317" opacity="0.45"/>
  <path class="d-line" d="M490 388 L694 317" opacity="0.45"/>
  <path class="d-line" d="M490 388 L762 317" opacity="0.45"/>
  <path class="d-line" d="M558 388 L150 317" opacity="0.45"/>
  <path class="d-line" d="M558 388 L218 317" opacity="0.45"/>
  <path class="d-line" d="M558 388 L286 317" opacity="0.45"/>
  <path class="d-line" d="M558 388 L422 317" opacity="0.45"/>
  <path class="d-line" d="M558 388 L490 317" opacity="0.45"/>
  <path class="d-line" d="M558 388 L558 317" opacity="0.45"/>
  <path class="d-line" d="M558 388 L626 317" opacity="0.45"/>
  <path class="d-line" d="M558 388 L694 317" opacity="0.45"/>
  <path class="d-line" d="M558 388 L762 317" opacity="0.45"/>
  <path class="d-line" d="M626 388 L150 317" opacity="0.45"/>
  <path class="d-line" d="M626 388 L218 317" opacity="0.45"/>
  <path class="d-line" d="M626 388 L286 317" opacity="0.45"/>
  <path class="d-line" d="M626 388 L422 317" opacity="0.45"/>
  <path class="d-line" d="M626 388 L490 317" opacity="0.45"/>
  <path class="d-line" d="M626 388 L558 317" opacity="0.45"/>
  <path class="d-line" d="M626 388 L626 317" opacity="0.45"/>
  <path class="d-line" d="M626 388 L694 317" opacity="0.45"/>
  <path class="d-line" d="M626 388 L762 317" opacity="0.45"/>
  <path class="d-line" d="M694 388 L150 317" opacity="0.45"/>
  <path class="d-line" d="M694 388 L218 317" opacity="0.45"/>
  <path class="d-line" d="M694 388 L286 317" opacity="0.45"/>
  <path class="d-line" d="M694 388 L422 317" opacity="0.45"/>
  <path class="d-line" d="M694 388 L490 317" opacity="0.45"/>
  <path class="d-line" d="M694 388 L558 317" opacity="0.45"/>
  <path class="d-line" d="M694 388 L626 317" opacity="0.45"/>
  <path class="d-line" d="M694 388 L694 317" opacity="0.45"/>
  <path class="d-line" d="M694 388 L762 317" opacity="0.45"/>
  <path class="d-line" d="M762 388 L150 317" opacity="0.45"/>
  <path class="d-line" d="M762 388 L218 317" opacity="0.45"/>
  <path class="d-line" d="M762 388 L286 317" opacity="0.45"/>
  <path class="d-line" d="M762 388 L422 317" opacity="0.45"/>
  <path class="d-line" d="M762 388 L490 317" opacity="0.45"/>
  <path class="d-line" d="M762 388 L558 317" opacity="0.45"/>
  <path class="d-line" d="M762 388 L626 317" opacity="0.45"/>
  <path class="d-line" d="M762 388 L694 317" opacity="0.45"/>
  <path class="d-line" d="M762 388 L762 317" opacity="0.45"/>
  <path class="d-line-accent" d="M150 388 L354 317"/>
  <path class="d-line-accent" d="M218 388 L354 317"/>
  <path class="d-line-accent" d="M286 388 L354 317"/>
  <path class="d-line-accent" d="M354 388 L354 317"/>
  <path class="d-line-accent" d="M422 388 L354 317"/>
  <path class="d-line-accent" d="M490 388 L354 317"/>
  <path class="d-line-accent" d="M558 388 L354 317"/>
  <path class="d-line-accent" d="M626 388 L354 317"/>
  <path class="d-line-accent" d="M694 388 L354 317"/>
  <path class="d-line-accent" d="M762 388 L354 317"/>
  <rect class="d-fill-soft" x="116" y="238" width="680" height="40" rx="5"/>
  <text class="d-label" x="456" y="255" text-anchor="middle">Blocks 2 … L: the same encoder block, repeated</text>
  <text class="d-note" x="456" y="269" text-anchor="middle">multi-head self-attention with no causal mask → Add &amp; Norm → feed-forward → Add &amp; Norm</text>
  <text class="d-label" x="20" y="209" text-anchor="start">Block L</text>
  <circle class="d-node d-role-input" cx="150" cy="400" r="12"/>
  <circle class="d-node d-role-feedforward" cx="150" cy="305" r="12"/>
  <path class="d-line" d="M150 293 V278"/>
  <path class="d-line" d="M150 238 V217"/>
  <circle class="d-node d-role-feedforward" cx="150" cy="205" r="12"/>
  <text class="d-note" x="150" y="208" text-anchor="middle">C</text>
  <circle class="d-node d-role-input" cx="218" cy="400" r="12"/>
  <circle class="d-node d-role-feedforward" cx="218" cy="305" r="12"/>
  <path class="d-line" d="M218 293 V278"/>
  <path class="d-line" d="M218 238 V217"/>
  <circle class="d-node d-role-feedforward" cx="218" cy="205" r="12"/>
  <text class="d-note" x="218" y="208" text-anchor="middle">T<tspan class="d-note" dy="3">1</tspan><tspan dy="-3"> </tspan></text>
  <circle class="d-node d-role-input" cx="286" cy="400" r="12"/>
  <circle class="d-node d-role-feedforward" cx="286" cy="305" r="12"/>
  <path class="d-line" d="M286 293 V278"/>
  <path class="d-line" d="M286 238 V217"/>
  <circle class="d-node d-role-feedforward" cx="286" cy="205" r="12"/>
  <text class="d-note" x="286" y="208" text-anchor="middle">T<tspan class="d-note" dy="3">2</tspan><tspan dy="-3"> </tspan></text>
  <circle class="d-node d-role-input" cx="354" cy="400" r="12"/>
  <circle class="d-node d-role-attention" cx="354" cy="305" r="12"/>
  <path class="d-line" d="M354 293 V278"/>
  <path class="d-line" d="M354 238 V217"/>
  <circle class="d-node d-role-feedforward" cx="354" cy="205" r="12"/>
  <text class="d-note" x="354" y="208" text-anchor="middle">T<tspan class="d-note" dy="3">3</tspan><tspan dy="-3"> </tspan></text>
  <circle class="d-node d-role-input" cx="422" cy="400" r="12"/>
  <circle class="d-node d-role-feedforward" cx="422" cy="305" r="12"/>
  <path class="d-line" d="M422 293 V278"/>
  <path class="d-line" d="M422 238 V217"/>
  <circle class="d-node d-role-feedforward" cx="422" cy="205" r="12"/>
  <text class="d-note" x="422" y="208" text-anchor="middle">T<tspan class="d-note" dy="3">4</tspan><tspan dy="-3"> </tspan></text>
  <circle class="d-node d-role-input" cx="490" cy="400" r="12"/>
  <circle class="d-node d-role-feedforward" cx="490" cy="305" r="12"/>
  <path class="d-line" d="M490 293 V278"/>
  <path class="d-line" d="M490 238 V217"/>
  <circle class="d-node d-role-feedforward" cx="490" cy="205" r="12"/>
  <text class="d-note" x="490" y="208" text-anchor="middle">T<tspan class="d-note" dy="3">5</tspan><tspan dy="-3"> </tspan></text>
  <circle class="d-node d-role-input" cx="558" cy="400" r="12"/>
  <circle class="d-node d-role-feedforward" cx="558" cy="305" r="12"/>
  <path class="d-line" d="M558 293 V278"/>
  <path class="d-line" d="M558 238 V217"/>
  <circle class="d-node d-role-feedforward" cx="558" cy="205" r="12"/>
  <text class="d-note" x="558" y="208" text-anchor="middle">T<tspan class="d-note" dy="3">6</tspan><tspan dy="-3"> </tspan></text>
  <circle class="d-node d-role-input" cx="626" cy="400" r="12"/>
  <circle class="d-node d-role-feedforward" cx="626" cy="305" r="12"/>
  <path class="d-line" d="M626 293 V278"/>
  <path class="d-line" d="M626 238 V217"/>
  <circle class="d-node d-role-feedforward" cx="626" cy="205" r="12"/>
  <text class="d-note" x="626" y="208" text-anchor="middle">T<tspan class="d-note" dy="3">7</tspan><tspan dy="-3"> </tspan></text>
  <circle class="d-node d-role-input" cx="694" cy="400" r="12"/>
  <circle class="d-node d-role-feedforward" cx="694" cy="305" r="12"/>
  <path class="d-line" d="M694 293 V278"/>
  <path class="d-line" d="M694 238 V217"/>
  <circle class="d-node d-role-feedforward" cx="694" cy="205" r="12"/>
  <text class="d-note" x="694" y="208" text-anchor="middle">T<tspan class="d-note" dy="3">8</tspan><tspan dy="-3"> </tspan></text>
  <circle class="d-node d-role-input" cx="762" cy="400" r="12"/>
  <circle class="d-node d-role-feedforward" cx="762" cy="305" r="12"/>
  <path class="d-line" d="M762 293 V278"/>
  <path class="d-line" d="M762 238 V217"/>
  <circle class="d-node d-role-feedforward" cx="762" cy="205" r="12"/>
  <text class="d-note" x="762" y="208" text-anchor="middle">T<tspan class="d-note" dy="3">9</tspan><tspan dy="-3"> </tspan></text>
  <path class="d-line-strong" d="M150 193 V158" marker-end="url(#bert-arrow-strong)"/>
  <rect class="d-box d-role-output" x="85" y="124" width="130" height="32" rx="5"/>
  <text class="d-label" x="150" y="138" text-anchor="middle">Next-sentence head</text>
  <text class="d-note" x="150" y="151" text-anchor="middle">IsNext / NotNext</text>
  <path class="d-line-accent" d="M354 193 V158" marker-end="url(#bert-arrow-accent)"/>
  <rect class="d-box d-role-output" x="276" y="124" width="156" height="32" rx="5"/>
  <text class="d-label" x="354" y="138" text-anchor="middle">Masked-LM head</text>
  <text class="d-note" x="354" y="151" text-anchor="middle">softmax over vocab → &quot;is&quot;</text>
  <text class="d-title" x="20" y="40" text-anchor="start">Pre-training on a sentence pair</text>
  <text class="d-math" x="20" y="62" text-anchor="start">loss = L<tspan class="d-note" dy="3">MLM</tspan><tspan dy="-3"> + L</tspan><tspan class="d-note" dy="3">NSP</tspan><tspan dy="-3"> </tspan></text>
  <text class="d-math" x="20" y="82" text-anchor="start">L<tspan class="d-note" dy="3">MLM</tspan><tspan dy="-3"> = −Σ</tspan><tspan class="d-note" dy="3">i∈M</tspan><tspan dy="-3"> log P(x</tspan><tspan class="d-note" dy="3">i</tspan><tspan dy="-3"> | x</tspan><tspan class="d-note" dy="3">∖M</tspan><tspan dy="-3">)</tspan></text>
  <text class="d-title" x="470" y="40" text-anchor="start">BERT-Base (Devlin et al. 2018)</text>
  <text class="d-note" x="470" y="58" text-anchor="start">L = 12 blocks · H = 768 hidden · A = 12 heads · 110M parameters</text>
  <text class="d-note" x="470" y="73" text-anchor="start">no causal mask: every token attends to every token, both directions</text>
  <text class="d-note" x="470" y="88" text-anchor="start">blue: the [MASK] position draws on words to its left and its right</text>
  <text class="d-note" x="470" y="103" text-anchor="start">MLM: 15% of tokens chosen; 80% → [MASK], 10% random, 10% unchanged</text>
  <text class="d-note" x="470" y="118" text-anchor="start">NSP: sentence B is the real next sentence 50% of the time</text>
  </svg>
diagram_caption: "BERT sums token, segment and position embeddings for a sentence pair, runs them through bidirectional encoder blocks where every token attends to every token, and pre-trains two heads: masked-word prediction and next-sentence prediction on [CLS]."
notable_models:
  - { name: "BERT-base / BERT-large", year: 2018, note: "The original — proved masked, bidirectional pretraining sets a new state of the art across NLP understanding benchmarks." }
  - { name: "RoBERTa", year: 2019, note: "Same architecture, better recipe — dropped next-sentence prediction and trained longer on more data; showed pretraining setup dominates." }
  - { name: "DistilBERT", year: 2019, note: "Distilled BERT to ~40% fewer parameters while keeping ~97% of performance — proved these encoders compress well." }
  - { name: "DeBERTa", year: 2020, note: "Disentangled content/position attention and a better mask decoder pushed the encoder line past human GLUE baselines." }
  - { name: "Sentence-BERT", year: 2019, note: "Adapted BERT to produce comparable sentence embeddings, making it practical for semantic search and clustering." }

resources:
  - { kind: paper, title: "BERT: Pre-training of Deep Bidirectional Transformers (Devlin et al., 2018)", url: "https://arxiv.org/abs/1810.04805" }
  - { kind: blog, title: "The Illustrated BERT, ELMo, and co. (Jay Alammar)", url: "https://jalammar.github.io/illustrated-bert/" }
  - { kind: blog, title: "Generalized Language Models (Lilian Weng)", url: "https://lilianweng.github.io/posts/2019-01-31-lm/" }
  - { kind: tutorial, title: "Hugging Face NLP Course — Transformer models", url: "https://huggingface.co/learn/nlp-course/chapter1/1" }

learn_first:
  - { label: "The Transformer (encoder half)", href: "/models/architecture/transformer", tag: "Architecture" }
  - { label: "GPT (the decoder-only complement)", href: "/models/architecture/gpt", tag: "Architecture" }
  - { label: "Softmax & cross-entropy loss", href: "/learn/concepts/softmax", tag: "Math" }
  - { label: "Subword tokenization (WordPiece/BPE)", href: "/learn/concepts/tokenization", tag: "Concept" }

tags: [bert, encoder, masked-lm, pretraining, llm]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/1810.04805", title: "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)", trust: 0.95 }
  - { url: "https://jalammar.github.io/illustrated-bert/", title: "The Illustrated BERT (Jay Alammar)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1907.11692", title: "RoBERTa: A Robustly Optimized BERT Pretraining Approach (Liu et al., 2019)", trust: 0.9 }
---

## What Changed
The transformer gave a powerful architecture, but early language models still read text left-to-right,
so a word's representation could only depend on what came before it. BERT keeps just the transformer's
**encoder** and trains it with **masked language modeling (MLM)**: randomly hide ~15% of tokens and ask
the model to reconstruct them from context on *both* sides.

This forces genuinely bidirectional representations — each token is encoded in light of its full
surroundings — which is exactly what understanding tasks (classification, entailment, question
answering) need. Just as important was the recipe it cemented: pretrain once on a huge unlabeled
corpus, then **fine-tune** a small task-specific head on each downstream dataset.

BERT swept the NLP benchmarks of its day and made "download a pretrained encoder and fine-tune" the
default workflow. It sits on the transformer branch; its encoder-only, understanding-first design is
the complement to the decoder-only, generation-first GPT line.

## Key Ideas
- **Encoder-only** — no autoregressive decoder; the goal is rich representations, not left-to-right
  generation.
- **Masked language modeling** — predicting hidden tokens from two-sided context yields deeply
  bidirectional embeddings, unlike causal LMs.
- **Pretrain then fine-tune** — one expensive unsupervised pretraining amortises across many cheap
  supervised fine-tunes, transferring general language knowledge.
- **Task heads** — `[CLS]`/token representations feed lightweight heads for classification, tagging,
  and span prediction; later work (RoBERTa) showed the pretraining recipe mattered more than the
  auxiliary next-sentence objective.
