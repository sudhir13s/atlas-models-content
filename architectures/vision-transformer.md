---
id: architecture-era/vision-transformer
type: architecture-era
slug: vision-transformer
schema_version: 1.0
title: "Vision Transformer (ViT)"
name: "Vision Transformer"
era: scaling
era_range: "2020–2023"
year: 2020
family: vision
core_innovation: "Treat an image as a sequence of patch embeddings and run a plain transformer over them — no convolutions required."
description: "The architecture that showed a pure transformer, given enough data, matches or beats CNNs on vision."
key_features: ["Image split into fixed patches", "Linear patch embeddings + position embeddings", "Standard transformer encoder", "[CLS] token for classification", "Scales with data and model size"]
key_models: ["ViT-B/L/H (2020)", "DeiT (2021)", "SAM / DINOv2 backbones (2023)"]
paper_title: "An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale"
paper_url: "https://arxiv.org/abs/2010.11929"
influenced_by: [architecture-era/transformer, architecture-era/cnn]
led_to: []

eli5: "Instead of scanning an image with sliding filters like older vision models, the Vision Transformer chops the picture into a grid of small square tiles, treats each tile like a word, and reads the whole grid at once — asking every tile which other tiles it should pay attention to. It is the same machine that powers language models, pointed at pictures."

analogy:
  title: "A mosaic where every tile can look at every other tile"
  body: "Picture a photo cut into a grid of square tiles, like a mosaic. A convolutional network only lets each tile whisper to its immediate neighbours, so it takes many layers for the top-left tile to 'hear' the bottom-right one. The Vision Transformer instead lays all the tiles on a table and lets each one glance at *every* other tile in a single step, deciding which are relevant — the sky tiles group together, the tiles making up a dog's face attend to each other across the whole image. That instant 'every tile sees every tile' is self-attention, borrowed straight from language transformers."

narrative:
  motivation: "For nearly a decade, vision meant convolution. The convolutional network's built-in assumptions — that useful patterns are local and appear anywhere — were treated as necessary priors for images, and they worked. Meanwhile transformers had taken over language completely by throwing such priors away and letting attention learn structure from data. The natural question: could a *pure* transformer, with none of vision's hand-wired assumptions, work on images too?"
  approach: "The obstacle is size. Attention compares every element with every other, so running it pixel-by-pixel on even a small image (tens of thousands of pixels) is hopelessly expensive. The fix is disarmingly simple: don't use pixels as the unit — use patches. Cut the image into a grid of fixed squares (say 16×16 pixels), and treat each patch as a single 'token', the way a transformer treats a word."
  solution: "Flatten each patch and linearly project it into an embedding vector — this is the patch's 'meaning'. Add a position embedding so the model knows where each patch sat in the grid (attention alone is order-blind). Prepend one extra learnable [CLS] token to summarise the whole image. Feed this short sequence into an ordinary transformer encoder — the exact stack of self-attention and feed-forward blocks built for language — and read the classification off the [CLS] token at the end."
  outcome: "With little vision-specific bias, ViT actually *underperforms* CNNs on small datasets — it has to learn locality that a CNN gets for free. But Dosovitskiy et al. showed that pretrained on enough data (tens to hundreds of millions of images), ViT *matches or beats* the best convolutional networks and keeps improving as data and model size grow. The deeper lesson: the transformer is a domain-agnostic substrate, and hard-wired priors can be *learned* from scale. ViT opened the door to unified multimodal models that treat image patches and text tokens identically."

build_steps:
  - { title: "1. Split the image into patches", detail: "Cut the input image into a regular grid of fixed-size squares — for a 224×224 image with 16×16 patches, that is 14×14 = 196 patches. Each patch will become one token." }
  - { title: "2. Flatten and embed each patch", detail: "Flatten each patch's pixels into a vector and pass it through one linear layer that projects it to the model's embedding size. This 'patch embedding' is the learned meaning of the tile." }
  - { title: "3. Add position embeddings", detail: "Self-attention has no built-in sense of where a patch was, so add a learned position embedding to each patch token — recording that this tile came from, say, the top-left of the grid." }
  - { title: "4. Prepend a [CLS] token", detail: "Add one extra learnable token to the front of the sequence. It attends to every patch and, by the final layer, becomes a summary vector for the whole image used for classification." }
  - { title: "5. Run the transformer encoder", detail: "Feed the sequence through a standard stack of transformer blocks — multi-head self-attention lets every token mix in context from every other, followed by a feed-forward layer, each wrapped in residual connections and normalization." }
  - { title: "6. Classify from the [CLS] token", detail: "Take the final [CLS] token, pass it through a small classification head (a linear layer + softmax), and read off the predicted class probabilities." }

key_equation:
  latex: "\\mathbf{z}_0 = [\\,\\mathbf{x}_{\\text{cls}};\\ \\mathbf{x}_p^1 E;\\ \\mathbf{x}_p^2 E;\\ \\dots;\\ \\mathbf{x}_p^N E\\,] + \\mathbf{E}_{\\text{pos}}"
  plain: "Build the transformer's input sequence: linearly embed each of the N flattened image patches (multiply patch xₚ by the shared projection E), prepend the learnable class token, and add a position embedding to every slot. This sequence z₀ is then fed to a standard transformer encoder — from here on it is exactly the language transformer's self-attention machinery, just over patches instead of words."
  terms:
    - { symbol: "z₀", meaning: "the input sequence handed to the transformer encoder" }
    - { symbol: "xₚⁱ", meaning: "the i-th flattened image patch (raw pixels of one tile)" }
    - { symbol: "E", meaning: "the shared linear projection that turns each patch into an embedding" }
    - { symbol: "x_cls", meaning: "the prepended learnable class token that summarises the whole image" }
    - { symbol: "E_pos", meaning: "the position embeddings added so the model knows where each patch sat" }

strengths:
  - "A single global attention step lets any patch relate to any other, capturing long-range structure that a CNN needs many layers to reach."
  - "Reuses the exact transformer stack from language, so tooling, scaling recipes, and hardware optimisations transfer directly."
  - "Scales gracefully — accuracy keeps climbing with more data and larger models, unlike CNNs that saturate sooner."
  - "The same substrate handles image patches and text tokens, enabling unified multimodal models."

weaknesses:
  - "Data-hungry: with weak vision priors it underperforms CNNs unless pretrained on very large datasets."
  - "Self-attention cost grows quadratically with the number of patches, making high-resolution images expensive."
  - "Little built-in translation or locality bias, so it must learn from data what a CNN assumes for free."
  - "Fixed patch grids can miss fine detail and struggle with objects at very different scales."

diagram: |
  flowchart LR
    IMG["Input image"] --> SPLIT["Split into fixed patches"]
    SPLIT --> EMB["Linear patch embeddings"]
    POS["Position embeddings"] --> ADD["＋"]
    EMB --> ADD
    CLS["[CLS] token"] --> ADD
    ADD --> ENC["Transformer encoder (self-attention)"]
    ENC --> HEAD["Classification head on [CLS]"]
    HEAD --> OUT["Class probabilities"]
diagram_caption: "ViT turns an image into a short sequence of patch tokens plus a class token, then runs the standard transformer encoder and reads the answer off the class token."

notable_models:
  - { name: "ViT-B/L/H", year: 2020, note: "The original — proved a pure transformer, pretrained at scale, matches or beats CNNs on ImageNet." }
  - { name: "DeiT", year: 2021, note: "Showed ViTs can be trained data-efficiently on ImageNet alone using distillation, without giant pretraining sets." }
  - { name: "Swin Transformer", year: 2021, note: "Reintroduced a locality prior via shifted windows, making ViTs efficient for detection and segmentation." }
  - { name: "DINOv2", year: 2023, note: "Proved self-supervised ViT backbones learn general-purpose visual features that transfer without labels." }
  - { name: "SAM (Segment Anything)", year: 2023, note: "Used a ViT backbone to build a promptable, zero-shot image segmentation foundation model." }

resources:
  - { kind: paper, title: "An Image is Worth 16x16 Words (Dosovitskiy et al., 2020)", url: "https://arxiv.org/abs/2010.11929" }
  - { kind: blog, title: "The Illustrated Transformer (Jay Alammar) — the engine ViT reuses", url: "https://jalammar.github.io/illustrated-transformer/" }
  - { kind: tutorial, title: "Vision Transformer (ViT) — Hugging Face docs", url: "https://huggingface.co/docs/transformers/model_doc/vit" }
  - { kind: video, title: "An Image is Worth 16x16 Words — ViT explained (Yannic Kilcher)", url: "https://www.youtube.com/watch?v=TrdevFK_am4" }

learn_first:
  - { label: "The Transformer & self-attention", href: "/models/architecture/transformer", tag: "Architecture" }
  - { label: "Convolutional neural networks (what ViT replaces)", href: "/models/architecture/cnn", tag: "Architecture" }
  - { label: "Embeddings & token representations", href: "/learn/concepts/embeddings", tag: "Concept" }

tags: [vit, vision, transformer, patches, scaling]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/2010.11929", title: "An Image is Worth 16x16 Words (Dosovitskiy et al., 2020)", trust: 0.95 }
  - { url: "https://huggingface.co/docs/transformers/model_doc/vit", title: "Vision Transformer (ViT) — Hugging Face documentation", trust: 0.9 }
---

## What Changed
Vision had been convolutional for a decade — locality and weight sharing were assumed necessary priors
for images. The Vision Transformer discarded them. It cuts an image into a grid of fixed patches (e.g.
16×16 pixels), linearly projects each patch into a token, adds a positional embedding, and feeds the
resulting sequence into an ordinary transformer encoder — the exact machinery built for language. With
little vision-specific inductive bias, ViT *underperforms* CNNs on small datasets, but Dosovitskiy et
al. showed that with enough pretraining data (tens of millions to hundreds of millions of images) it
**matches or surpasses** the best convolutional networks, and keeps improving as data and model size
grow. The lesson generalised the transformer's reach: attention over tokens is a domain-agnostic
substrate, and the CNN's hard-wired priors can be *learned* from data instead. It descends from both
the transformer (the engine) and the CNN (the task and benchmarks it had to beat), and it opened the
door to unified multimodal models that treat image patches and text tokens the same way.

## Key Ideas
- **Patch tokenization** — an image becomes a short sequence of patch embeddings, making the
  quadratic cost of attention tractable (hundreds of tokens, not tens of thousands of pixels).
- **Minimal inductive bias** — no convolutions or pooling; the model must *learn* locality and
  translation behaviour, which needs more data but generalises further.
- **Data-hungry crossover** — CNNs win in the low-data regime; ViT overtakes them once pretraining
  data is large, illustrating the bias-versus-scale trade-off.
- **Unified substrate** — the same transformer handles patches and words, enabling the multimodal
  models that jointly process vision and language.
