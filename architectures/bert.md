---
id: architecture-era/bert
type: architecture-era
slug: bert
schema_version: 2.5
title: "BERT (Bidirectional Pretraining)"
name: "BERT"
era: scaling
era_range: "2018–2020"
year: 2018
family: llm
core_innovation: "Encoder-only bidirectional pretraining via masked language modeling, producing reusable contextual representations for fine-tuning."
description: "A Transformer encoder that reads a whole passage at once, pre-trained on unlabelled text by guessing hidden words from the words on both sides, then fine-tuned with one small output layer for each task."
key_features: ["Encoder-only transformer", "Masked language modeling", "Bidirectional context", "Fine-tune per downstream task"]
key_models: ["BERT-base/large (2018)", "RoBERTa (2019)", "DeBERTa (2020)"]
paper_title: "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding"
paper_url: "https://arxiv.org/abs/1810.04805"
influenced_by: [architecture-era/transformer]
led_to: []

analogy:
  title: "A fill-in-the-blank test with both sides visible"
  body: "This is an analogy, not the mechanism. In a fill-in-the-blank test such as 'the man went to ___ store', you read the words before the blank and the words after it. BERT is trained on millions of such blanks made from ordinary text and graded on each guess; its authors name this Cloze task as the inspiration. A left-to-right model reads with a card covering everything after the blank; BERT reads the whole line. Unlike a pupil, BERT does not write one answer: it gives a probability to every word in its vocabulary, as the worked example shows."

narrative:
  motivation: "**Pre-trained language models read in one direction.** The paper is by **Devlin et al. (2018)** at Google AI Language.\n\n- **The limit:** in OpenAI GPT every token can attend only to the tokens on its **left**; the authors argue this is sub-optimal for sentence-level tasks and could be very harmful for token-level tasks such as **question answering**.\n- **The obstacle:** a standard language model cannot simply be trained in both directions, because each word could then indirectly **see itself** and predict itself trivially."
  approach: "**Hide some words and predict them.**\n\n- **Masked language model (MLM):** **15%** of the WordPiece tokens in each sequence are chosen at random and predicted from the rest; the authors name the **Cloze task** (Taylor, 1953) as the inspiration.\n- **The 80/10/10 rule:** a chosen token becomes **[MASK]** 80% of the time, a **random token** 10% and stays **unchanged** 10%, because [MASK] never appears in fine-tuning.\n- **Next-sentence prediction (NSP):** for a pair of text spans A and B, B is the real next span **50%** of the time; the model classifies the pair as IsNext or NotNext."
  solution: "**An encoder with no causal mask, pre-trained once and fine-tuned per task.**\n\n- **Two sizes:** **BERT-Base** (12 layers, 768-number vectors, 12 heads, **110M** parameters) and **BERT-Large** (24, 1,024, 16, **340M**); Base was chosen to be the **same size as OpenAI GPT**.\n- **Data:** **BooksCorpus** (800M words) and **English Wikipedia** (2,500M words).\n- **Training:** **1,000,000** steps of **256** sequences, about **40 epochs** over the 3.3 billion words; **4 days** on 16 TPU chips (Base) or 64 (Large).\n- **Fine-tuning:** all parameters are fine-tuned with one output layer; every result in the paper can be reproduced in at most **1 hour** on one Cloud TPU."
  outcome: "**New state-of-the-art results on eleven tasks**, the authors report:\n\n- **GLUE:** a score of **80.5**, **7.7** points above the previous best.\n- **MultiNLI:** **86.7%** accuracy, **+4.6** points.\n- **SQuAD v1.1:** test F1 **93.2**, **+1.5**. **SQuAD v2.0:** test F1 **83.1**, **+5.1**.\n\n**What came next:**\n\n- **RoBERTa (Liu et al., 2019):** found BERT **significantly undertrained**; it trained longer, with bigger batches, over more data and **without NSP**.\n- **Smaller and sentence-level descendants:** DistilBERT and Sentence-BERT (see Notable models)."

key_equation:
  latex: "\\mathcal{L}_{\\mathrm{MLM}}=-\\frac{1}{|M|}\\sum_{i\\in M}\\log P\\!\\left(x_i \\mid \\tilde{x}\\right)"
  plain: "In words: for every chosen position, take the probability the model gives the original token, given the whole corrupted sequence on both sides, and average the negative logarithms. The paper states it in words: each chosen token's final vector predicts the original token with a cross-entropy loss, and the pre-training loss adds the mean masked-LM term to the mean next-sentence term. The worked example computes one such term by hand."
  terms:
    - { symbol: "\\mathcal{L}_{\\mathrm{MLM}}", meaning: "the masked-language-model loss that training makes small" }
    - { symbol: "M", meaning: "the positions chosen for prediction: 15% of the tokens in each sequence" }
    - { symbol: "|M|", meaning: "how many positions were chosen, so the sum becomes a mean" }
    - { symbol: "x_i", meaning: "the original token at chosen position i" }
    - { symbol: "\\tilde{x}", meaning: "the input after masking: each chosen token replaced by [MASK], by a random token, or left as it was (80%, 10%, 10%)" }
    - { symbol: "P(x_i \\mid \\tilde{x})", meaning: "the model's probability for the original token: one entry of the softmax over the 30,000-token vocabulary" }

strengths:
  - "**Context from both sides in every layer:** the authors write that, of BERT, OpenAI GPT and ELMo, **only BERT's** representations are jointly conditioned on left and right context **in all layers**."
  - "**One model, many tasks:** for GLUE the only new parameters are one classification matrix of size **K × H** (K labels); every fine-tuning result in the paper takes at most **1 hour** on one Cloud TPU."
  - "**Bidirectionality measured:** trained left-to-right as in GPT, and without next-sentence prediction, the same BERT-Base drops from **88.5** to **77.8** F1 on SQuAD and from **86.7** to **77.5** on MRPC (the paper's Table 5)."
  - "**Results:** new state of the art on **eleven** tasks, including GLUE **80.5** and SQuAD v1.1 test F1 **93.2**."

weaknesses:
  - "**A token never seen in use:** [MASK] does not appear during fine-tuning; the authors reduce this **mismatch** with the 80/10/10 rule."
  - "**Fewer predictions per batch:** only **15%** of tokens are predicted, and masked-LM pre-training converges **slightly slower** than left-to-right training, though it outperforms it in accuracy almost immediately, the authors report."
  - "**Short inputs:** a pre-training input holds at most **512** tokens; the authors note attention cost is **quadratic** in sequence length and pre-train with length **128** for 90% of the steps."
  - "**Undertrained recipe:** RoBERTa's authors found BERT **significantly undertrained**."
  - "**Slow for similarity search:** the Sentence-BERT authors report that finding the most similar pair among **10,000** sentences takes about **50 million** BERT passes, about **65 hours**."

visual:
  kind: distribution
  tokens: ["the", "a", "his", "school"]
  logits: [2.5, 1.5, 1.0, -1.0]
  temperatures: [1]
  panel_titles: ["Probability of each candidate at the [MASK] position"]
  caption: "BERT's prediction at the masked position of \"the man went to [MASK] store\", over four candidate words, computed from the example scores 2.5, 1.5, 1.0 and −1.0 of the worked example. A real model scores all 30,000 entries of its vocabulary. The scores are an example, not from a trained model."

comparison:
  before: "OpenAI GPT (2018)"
  after: "BERT (2018)"
  rows:
    - { aspect: "What each token can attend to", before: "Only the tokens to its left", after: "Every token, left and right", source: "https://arxiv.org/abs/1810.04805" }
    - { aspect: "Pre-training task", before: "Left-to-right language modelling: predict the next token", after: "Masked language model and next-sentence prediction", source: "https://arxiv.org/abs/1810.04805" }
    - { aspect: "Size", before: "12 layers, 768-number vectors, 12 heads", after: "BERT-Base: the same size, chosen for comparison (BERT-Large: 24, 1,024, 16)", source: "https://arxiv.org/abs/1810.04805" }
    - { aspect: "Pre-training text", before: "BooksCorpus (800M words)", after: "BooksCorpus and English Wikipedia (800M + 2,500M words)", source: "https://arxiv.org/abs/1810.04805" }
    - { aspect: "[CLS], [SEP] and sentence A/B embeddings", before: "Introduced only at fine-tuning", after: "Learned during pre-training", source: "https://arxiv.org/abs/1810.04805" }
    - { aspect: "Training steps and batch", before: "1M steps of 32,000 words", after: "1M steps of 128,000 words", source: "https://arxiv.org/abs/1810.04805" }
    - { aspect: "Official GLUE score", before: "72.8", after: "80.5 (BERT-Large)", source: "https://arxiv.org/abs/1810.04805" }

key_numbers:
  columns: ["Setting", "BERT-Base", "BERT-Large"]
  rows:
    - { cells: ["Layers (L)", "12", "24"], source: "https://arxiv.org/abs/1810.04805" }
    - { cells: ["Numbers in each token's vector (H)", "768", "1,024"], source: "https://arxiv.org/abs/1810.04805" }
    - { cells: ["Attention heads (A)", "12", "16"], source: "https://arxiv.org/abs/1810.04805" }
    - { cells: ["Inner width of the feed-forward network (4H)", "3,072", "4,096"], source: "https://arxiv.org/abs/1810.04805" }
    - { cells: ["Parameters", "110M", "340M"], source: "https://arxiv.org/abs/1810.04805" }
    - { cells: ["WordPiece vocabulary", "30,000 tokens", "30,000 tokens"], source: "https://arxiv.org/abs/1810.04805" }
    - { cells: ["Longest pre-training input", "512 tokens", "512 tokens"], source: "https://arxiv.org/abs/1810.04805" }
    - { cells: ["Pre-training", "1,000,000 steps of 256 sequences; 4 days on 16 TPU chips", "1,000,000 steps of 256 sequences; 4 days on 64 TPU chips"], source: "https://arxiv.org/abs/1810.04805" }
    - { cells: ["GLUE test average (the paper's Table 1, without WNLI)", "79.6", "82.1"], source: "https://arxiv.org/abs/1810.04805" }
    - { cells: ["MultiNLI accuracy, matched test set", "84.6", "86.7"], source: "https://arxiv.org/abs/1810.04805" }
    - { cells: ["SQuAD v1.1 dev F1, single model", "88.5", "90.9"], source: "https://arxiv.org/abs/1810.04805" }

misunderstandings:
  - misunderstanding: "Every word BERT has to predict is replaced by the [MASK] token."
    truth: "Of the **15%** of positions chosen, **80%** become [MASK], **10%** a random token and **10%** stay unchanged. Random replacement touches only **1.5%** of all tokens (10% of 15%). Because the model cannot tell which tokens were swapped, the authors write, it must keep a contextual representation of **every** input token."
    source: "https://arxiv.org/abs/1810.04805"
  - misunderstanding: "BERT reads left-to-right and right-to-left separately and joins the two, like ELMo."
    truth: "That is ELMo: a **concatenation** of independently trained left-to-right and right-to-left LSTMs. BERT is one Transformer encoder whose representations are **jointly conditioned** on left and right context **in all layers**. The authors add that the ELMo approach is twice as expensive and strictly less powerful than a deep bidirectional model."
    source: "https://arxiv.org/abs/1810.04805"
  - misunderstanding: "The [CLS] output of a pre-trained BERT is a ready-made sentence embedding."
    truth: "The paper states that the vector C of [CLS] is **not a meaningful sentence representation without fine-tuning**, because it was trained only for next-sentence prediction. Sentence-BERT (2019) later fine-tuned BERT in siamese and triplet networks to produce embeddings compared by **cosine similarity**."
    source: "https://arxiv.org/abs/1810.04805"
  - misunderstanding: "BERT writes text the way GPT does."
    truth: "BERT is pre-trained to recover masked tokens from **both sides**, not to continue a text from left to right. The paper notes that the left-context-only Transformer is the one called a **decoder**, because it can be used for text generation. Every task in the paper puts a small output layer on BERT's vectors: classes from **[CLS]**, answer spans from the **token vectors**."
    source: "https://arxiv.org/abs/1810.04805"

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
  - { name: "BERT-base / BERT-large", year: 2018, note: "The original (110M and 340M parameters): new state-of-the-art results on eleven NLP tasks." }
  - { name: "RoBERTa", year: 2019, note: "A replication study: training longer, with bigger batches, over more data and without next-sentence prediction, matching or exceeding models published after BERT." }
  - { name: "DistilBERT", year: 2019, note: "Knowledge distillation during pretraining: 40% smaller and 60% faster while retaining 97% of BERT's language understanding, per the authors." }
  - { name: "DeBERTa", year: 2020, note: "Disentangled content and position attention plus an enhanced mask decoder; a 1.5B-parameter single model surpassed the human baseline on SuperGLUE (89.9 vs 89.8)." }
  - { name: "Sentence-BERT", year: 2019, note: "Siamese and triplet BERT networks that produce sentence embeddings comparable by cosine similarity, for semantic search and clustering." }

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
updated: '2026-11-23'
sources:
  - { url: "https://arxiv.org/abs/1810.04805", title: "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1907.11692", title: "RoBERTa: A Robustly Optimized BERT Pretraining Approach (Liu et al., 2019)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1910.01108", title: "DistilBERT, a distilled version of BERT: smaller, faster, cheaper and lighter (Sanh et al., 2019)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2006.03654", title: "DeBERTa: Decoding-enhanced BERT with Disentangled Attention (He et al., 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1908.10084", title: "Sentence-BERT: Sentence Embeddings using Siamese BERT-Networks (Reimers and Gurevych, 2019)", trust: 0.95 }
---

## What Changed

The authors write that many design decisions in BERT were **made to keep it as close to OpenAI
GPT as possible**, so the two could be compared. The table above lists what still differs.

How to read it (it follows Sections 3 and A.4 of the BERT paper):

- **What each token can attend to** is the change the paper is named for: GPT masks out every
  later token, BERT masks out none.
- **The pre-training task** has to change with it: a model that sees both sides could read the
  next word directly, so BERT hides words instead of predicting the next one.
- **The other rows** are differences in training data and recipe. The authors test them in their
  Table 5: the same BERT-Base trained left-to-right, without next-sentence prediction, is worse on
  every task listed, with large drops on MRPC and SQuAD.
- **The GLUE score** is the official leaderboard score; GPT's 72.8 is the value the BERT authors
  quote as of their writing.

## Key Ideas

- **Encoder-only:** no causal mask, so every token's vector is built from the whole input.
- **Masked language modelling:** predicting hidden tokens from both sides is what makes that
  bidirectional training possible.
- **Pre-train, then fine-tune:** one unsupervised pre-training run serves many supervised tasks.
- **Small task heads:** [CLS] and the token vectors feed one output layer for classification,
  tagging or answer spans.

## How It Works

The figure above shows BERT pre-training on a pair of text spans. Follow it from the bottom.

A **token** is a piece of text, a word or part of a word. A **vector** is a list of numbers; in
BERT-Base every token is carried as a vector of **768** numbers (called H).

1. **Input tokens.** Text is cut into **WordPiece** tokens from a vocabulary of **30,000**. A
   **[CLS]** token always comes first, a **[SEP]** token closes each span, and the two spans
   together hold at most **512** tokens.
2. **Token + segment + position.** Each token's input vector is the **sum of three learned
   embeddings**: one for the token, one saying whether it belongs to span **A or B**, and one for
   its **position** (the three rows of boxes).
3. **Masking (pre-training only).** **15%** of the positions are chosen at random. In the figure
   the token "is" at position 3 has become **[MASK]**; the model must recover it.
4. **Block 1: all-to-all attention.** Self-attention with **no causal mask**: every token attends
   to every other token. The blue lines show the [MASK] position drawing on words to its **left
   and its right**.
5. **Blocks 2 to L.** The same encoder block repeats: multi-head self-attention, Add & Norm,
   a feed-forward network (**768 → 3,072 → 768** with the GELU activation), Add & Norm. BERT-Base
   stacks **12** blocks with **12** heads; BERT-Large **24** blocks with **16** heads.
6. **Outputs.** The last block gives one vector per token: **C** for [CLS] and **T₁ … T₉** for
   the others.
7. **Masked-LM head.** The vector at each chosen position goes into a **softmax over the
   vocabulary**, and a cross-entropy loss rewards a high probability for the original token (the
   worked example).
8. **Next-sentence head.** **C** is classified as IsNext or NotNext; B is the real next span half
   of the time. The pre-training loss is the **mean masked-LM loss plus the mean next-sentence
   loss**.
9. **Fine-tuning.** The same network, with all its parameters, is trained further on a labelled
   task with **one new output layer**. A classification task uses C with a K × H matrix (K
   labels). SQuAD learns a **start vector S** and an **end vector E**: the chance that token i
   starts the answer is a softmax of S · Tᵢ over the passage.

## Worked Example

**One masked word, predicted and scored.** The sentence is from the paper's own next-sentence
example. All scores are an example, not from a trained model; they are small so every step can be
checked by hand.

**Step 1: choose and mask positions.** Out of every **1,000** tokens:

- **15%** are chosen: 1,000 × 0.15 = **150** positions.
- Of those, 80% become [MASK]: 150 × 0.8 = **120**.
- 10% become a random token: 150 × 0.1 = **15**, which is 15 / 1,000 = **1.5%** of all tokens,
  the figure the paper gives.
- 10% stay unchanged: **15**. All 150 are still predicted.

**Step 2: score the candidates.** The input is "the man went to [MASK] store". The final vector at
[MASK] is scored against each vocabulary entry; here four of them: the **2.5**, a **1.5**, his
**1.0**, school **−1.0**.

- A left-to-right model would see only "the man went to"; BERT also sees **"store"**, which fits
  "the" and not "school".

**Step 3: softmax** (what the figure draws):

1. $e^{2.5} \approx 12.182$, $e^{1.5} \approx 4.482$, $e^{1.0} \approx 2.718$ and
   $e^{-1.0} \approx 0.368$; the total is 19.750.
2. Probabilities: the **0.617**, a **0.227**, his **0.138**, school **0.019**. They add up to 1,
   up to rounding.

**Step 4: the loss.** The original token was "the", so this position adds
$-\ln 0.617 \approx$ **0.483** to the masked-LM loss. Had the model given "the" only 0.25, the term
would be $-\ln 0.25 \approx 1.386$. The loss of a sequence is the **mean** of these terms over its
chosen positions.

**What this means:** BERT learns by being graded on hidden words, using the words on **both
sides** of each one. Only the chosen 15% are graded, which is why the authors found it converges
slightly slower than a left-to-right model that is graded on every token.
