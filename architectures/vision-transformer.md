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
description: "A pure transformer applied to sequences of image patches; pretrained on enough data, it approaches or beats state-of-the-art convolutional networks on image recognition."
key_features: ["Image split into fixed patches", "Linear patch embeddings + position embeddings", "Transformer encoder over patch tokens", "Scales with data and model size"]
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
  motivation: "In vision, attention was applied together with convolutional networks or used to replace parts of them while keeping their overall structure. Convolutional networks build in locality and translation equivariance. Meanwhile the Transformer had become the de facto standard for natural language processing. The question: could a *pure* transformer, without those image-specific assumptions, work on images too?"
  approach: "The obstacle is size. Attention compares every element with every other, so applying it pixel by pixel has a cost quadratic in the number of pixels. The fix: use patches, not pixels, as the unit. Cut the image into a grid of fixed squares (for example 16×16 pixels), and treat each patch as a single 'token', the way a transformer treats a word."
  solution: "Flatten each patch and linearly project it into an embedding vector — this is the patch's 'meaning'. Add a position embedding so the model knows where each patch sat in the grid (attention alone is order-blind). Prepend one extra learnable [CLS] token to summarise the whole image. Feed this short sequence into an ordinary transformer encoder — the exact stack of self-attention and feed-forward blocks built for language — and read the classification off the [CLS] token at the end."
  outcome: "Trained on mid-sized datasets such as ImageNet without strong regularization, ViT is a few percentage points below ResNets of comparable size. Pretrained on larger datasets (14M–300M images), Dosovitskiy et al. report that it approaches or beats the state of the art on multiple image recognition benchmarks; in their words, large-scale training trumps inductive bias."

build_steps:
  - { title: "1. Split the image into patches", detail: "Cut the input image into a regular grid of fixed-size squares — for a 224×224 image with 16×16 patches, that is 14×14 = 196 patches. Each patch will become one token." }
  - { title: "2. Flatten and embed each patch", detail: "Flatten each patch's pixels into a vector and pass it through one linear layer that projects it to the model's embedding size. This 'patch embedding' is the learned meaning of the tile." }
  - { title: "3. Add position embeddings", detail: "Self-attention has no built-in sense of where a patch was, so add a learned position embedding to each patch token — recording that this tile came from, say, the top-left of the grid." }
  - { title: "4. Prepend a [CLS] token", detail: "Add one extra learnable token to the front of the sequence. It attends to every patch and, by the final layer, becomes a summary vector for the whole image used for classification." }
  - { title: "5. Run the transformer encoder", detail: "Feed the sequence through a standard stack of transformer blocks — multi-head self-attention lets every token mix in context from every other, followed by a feed-forward layer, each wrapped in residual connections and normalization." }
  - { title: "6. Classify from the [CLS] token", detail: "Take the final [CLS] token, pass it through a small classification head (one hidden layer during pre-training, a single linear layer when fine-tuning, then softmax), and read off the predicted class probabilities." }

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
  - "Self-attention layers are global, so any patch can relate to any other in a single layer."
  - "Reuses the NLP transformer: the authors note scalable NLP Transformer architectures and their efficient implementations can be used almost out of the box."
  - "Benefits from scale: the full benefit of larger models appears only with the largest pretraining dataset (JFT-300M), per the authors."

weaknesses:
  - "Data-hungry: trained on mid-sized datasets without strong regularization it trails comparable ResNets."
  - "Self-attention cost grows quadratically with the number of patches, making high-resolution images expensive."
  - "Much less image-specific inductive bias (locality, translation equivariance) than a CNN, so it must learn these from data."
  - "Swin Transformer's authors point to large variations in the scale of visual entities and high pixel resolution as challenges in adapting the Transformer to vision."

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 900 520" role="img" aria-label="Vision Transformer: an image is cut into 16 by 16 patches, each patch is flattened and linearly projected into a token, a learnable class token is prepended and position embeddings are added; the sequence passes through L pre-norm encoder blocks of LayerNorm, multi-head self-attention, residual add, LayerNorm, MLP and residual add, and an MLP head reads the final class token to predict the class.">
  <defs><marker id="vit-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="vit-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <text class="d-title" x="86" y="50" text-anchor="middle">Image</text>
  <rect class="d-box d-role-input" x="30" y="64" width="112" height="112"/>
  <path class="d-line" d="M58 64 V176 M30 92 H142"/>
  <path class="d-line" d="M86 64 V176 M30 120 H142"/>
  <path class="d-line" d="M114 64 V176 M30 148 H142"/>
  <rect class="d-box d-role-attention" x="30" y="64" width="28" height="28"/>
  <text class="d-note" x="86" y="192" text-anchor="middle">224×224 → 14×14 grid</text>
  <text class="d-note" x="86" y="205" text-anchor="middle">N = 196 patches (4×4 drawn)</text>
  <text class="d-label" x="420" y="52" text-anchor="middle">16×16 patches, each flattened to 16·16·3 = 768 values</text>
  <path class="d-line-strong" d="M148 79 H247" marker-end="url(#vit-arrow)"/>
  <rect class="d-box d-role-attention" x="253" y="66" width="26" height="26"/>
  <path class="d-line" d="M266 92 V118"/>
  <rect class="d-box d-role-input" x="297" y="66" width="26" height="26"/>
  <path class="d-line" d="M310 92 V118"/>
  <rect class="d-box d-role-input" x="341" y="66" width="26" height="26"/>
  <path class="d-line" d="M354 92 V118"/>
  <rect class="d-box d-role-input" x="385" y="66" width="26" height="26"/>
  <path class="d-line" d="M398 92 V118"/>
  <rect class="d-box d-role-input" x="429" y="66" width="26" height="26"/>
  <path class="d-line" d="M442 92 V118"/>
  <rect class="d-box d-role-input" x="473" y="66" width="26" height="26"/>
  <path class="d-line" d="M486 92 V118"/>
  <text class="d-title" x="530" y="83" text-anchor="middle">…</text>
  <rect class="d-box d-role-input" x="561" y="66" width="26" height="26"/>
  <path class="d-line" d="M574 92 V118"/>
  <rect class="d-box d-role-embedding" x="245" y="120" width="350" height="26" rx="4"/>
  <text class="d-label" x="420" y="137" text-anchor="middle">linear projection E (shared): 768 → D = 768</text>
  <rect class="d-box d-role-output" x="213" y="162" width="18" height="36" rx="3"/>
  <text class="d-label" x="222" y="214" text-anchor="middle">+</text>
  <rect class="d-box d-role-position" x="213" y="224" width="18" height="18" rx="3"/>
  <text class="d-note" x="222" y="237" text-anchor="middle">0</text>
  <path class="d-line-strong" d="M266 146 V160" marker-end="url(#vit-arrow)"/>
  <rect class="d-box d-role-embedding" x="257" y="162" width="18" height="36" rx="3"/>
  <text class="d-label" x="266" y="214" text-anchor="middle">+</text>
  <rect class="d-box d-role-position" x="257" y="224" width="18" height="18" rx="3"/>
  <text class="d-note" x="266" y="237" text-anchor="middle">1</text>
  <path class="d-line-strong" d="M310 146 V160" marker-end="url(#vit-arrow)"/>
  <rect class="d-box d-role-embedding" x="301" y="162" width="18" height="36" rx="3"/>
  <text class="d-label" x="310" y="214" text-anchor="middle">+</text>
  <rect class="d-box d-role-position" x="301" y="224" width="18" height="18" rx="3"/>
  <text class="d-note" x="310" y="237" text-anchor="middle">2</text>
  <path class="d-line-strong" d="M354 146 V160" marker-end="url(#vit-arrow)"/>
  <rect class="d-box d-role-embedding" x="345" y="162" width="18" height="36" rx="3"/>
  <text class="d-label" x="354" y="214" text-anchor="middle">+</text>
  <rect class="d-box d-role-position" x="345" y="224" width="18" height="18" rx="3"/>
  <text class="d-note" x="354" y="237" text-anchor="middle">3</text>
  <path class="d-line-strong" d="M398 146 V160" marker-end="url(#vit-arrow)"/>
  <rect class="d-box d-role-embedding" x="389" y="162" width="18" height="36" rx="3"/>
  <text class="d-label" x="398" y="214" text-anchor="middle">+</text>
  <rect class="d-box d-role-position" x="389" y="224" width="18" height="18" rx="3"/>
  <text class="d-note" x="398" y="237" text-anchor="middle">4</text>
  <path class="d-line-strong" d="M442 146 V160" marker-end="url(#vit-arrow)"/>
  <rect class="d-box d-role-embedding" x="433" y="162" width="18" height="36" rx="3"/>
  <text class="d-label" x="442" y="214" text-anchor="middle">+</text>
  <rect class="d-box d-role-position" x="433" y="224" width="18" height="18" rx="3"/>
  <text class="d-note" x="442" y="237" text-anchor="middle">5</text>
  <path class="d-line-strong" d="M486 146 V160" marker-end="url(#vit-arrow)"/>
  <rect class="d-box d-role-embedding" x="477" y="162" width="18" height="36" rx="3"/>
  <text class="d-label" x="486" y="214" text-anchor="middle">+</text>
  <rect class="d-box d-role-position" x="477" y="224" width="18" height="18" rx="3"/>
  <text class="d-note" x="486" y="237" text-anchor="middle">6</text>
  <text class="d-title" x="530" y="184" text-anchor="middle">…</text>
  <text class="d-title" x="530" y="237" text-anchor="middle">…</text>
  <path class="d-line-strong" d="M574 146 V160" marker-end="url(#vit-arrow)"/>
  <rect class="d-box d-role-embedding" x="565" y="162" width="18" height="36" rx="3"/>
  <text class="d-label" x="574" y="214" text-anchor="middle">+</text>
  <rect class="d-box d-role-position" x="565" y="224" width="18" height="18" rx="3"/>
  <text class="d-note" x="574" y="237" text-anchor="middle">N</text>
  <text class="d-title" x="205" y="177" text-anchor="end">[class]</text>
  <text class="d-note" x="205" y="191" text-anchor="end">learnable</text>
  <text class="d-label" x="604" y="184">tokens xₚⁱE, one per patch</text>
  <text class="d-label" x="604" y="237">+ learned 1-D position embeddings</text>
  <text class="d-title" x="604" y="52">Encoder input (Eq. 1)</text>
  <text class="d-math" x="604" y="72">z₀ = [x_class; xₚ¹E; …; xₚᴺE] + E_pos</text>
  <text class="d-note" x="604" y="90">a sequence of N + 1 = 197 tokens, each D = 768</text>
  <text class="d-note" x="604" y="116">ViT-Base/16: L = 12 blocks, D = 768,</text>
  <text class="d-note" x="604" y="129">12 heads, MLP 3072, 86M parameters</text>
  <path class="d-line-strong" d="M398 242 V272 H12 V392 H50" marker-end="url(#vit-arrow)"/>
  <text class="d-math" x="408" y="266">z₀</text>
  <rect class="d-frame" x="24" y="300" width="620" height="146" rx="8"/>
  <text class="d-title" x="38" y="320">Transformer encoder block (pre-norm)</text>
  <text class="d-title" x="630" y="320" text-anchor="end">× L (L = 12)</text>
  <circle class="d-node" cx="64" cy="392" r="2.5"/>
  <path class="d-line-accent" d="M64 392 V352 Q64 344 72 344 H332 Q340 344 340 352 V379" marker-end="url(#vit-arrow-accent)"/>
  <circle class="d-node" cx="364" cy="392" r="2.5"/>
  <path class="d-line-accent" d="M364 392 V352 Q364 344 372 344 H576 Q584 344 584 352 V379" marker-end="url(#vit-arrow-accent)"/>
  <rect class="d-box" x="82" y="374" width="58" height="36" rx="5"/>
  <text class="d-title" x="111" y="396" text-anchor="middle">Norm</text>
  <text class="d-note" x="111" y="426" text-anchor="middle">LayerNorm</text>
  <rect class="d-box d-role-attention" x="162" y="374" width="150" height="36" rx="5"/>
  <text class="d-title" x="237" y="396" text-anchor="middle">Multi-head attention</text>
  <text class="d-note" x="237" y="426" text-anchor="middle">self-attention, 12 heads</text>
  <rect class="d-box" x="384" y="374" width="58" height="36" rx="5"/>
  <text class="d-title" x="413" y="396" text-anchor="middle">Norm</text>
  <text class="d-note" x="413" y="426" text-anchor="middle">LayerNorm</text>
  <rect class="d-box d-role-feedforward" x="464" y="374" width="92" height="36" rx="5"/>
  <text class="d-title" x="510" y="396" text-anchor="middle">MLP</text>
  <text class="d-note" x="510" y="426" text-anchor="middle">768→3072→768, GELU</text>
  <path class="d-line-strong" d="M140 392 H160" marker-end="url(#vit-arrow)"/>
  <path class="d-line-strong" d="M312 392 H327" marker-end="url(#vit-arrow)"/>
  <circle class="d-node d-role-attention" cx="340" cy="392" r="11"/>
  <path class="d-line-strong" d="M335 392 H345 M340 387 V397"/>
  <path class="d-line-strong" d="M351 392 H382" marker-end="url(#vit-arrow)"/>
  <path class="d-line-strong" d="M442 392 H462" marker-end="url(#vit-arrow)"/>
  <path class="d-line-strong" d="M556 392 H571" marker-end="url(#vit-arrow)"/>
  <circle class="d-node d-role-attention" cx="584" cy="392" r="11"/>
  <path class="d-line-strong" d="M579 392 H589 M584 387 V397"/>
  <path class="d-line-strong" d="M50 392 H80" marker-end="url(#vit-arrow)"/>
  <text class="d-note" x="202" y="338" text-anchor="middle">residual</text>
  <text class="d-note" x="474" y="338" text-anchor="middle">residual</text>
  <text class="d-math" x="38" y="468">z′ₗ = MSA(LN(zₗ₋₁)) + zₗ₋₁</text>
  <text class="d-math" x="284" y="468">zₗ = MLP(LN(z′ₗ)) + z′ₗ,   ℓ = 1 … L</text>
  <path class="d-line-strong" d="M595 392 H663" marker-end="url(#vit-arrow)"/>
  <text class="d-math" x="676" y="326" text-anchor="middle">z_L</text>
  <rect class="d-box d-role-output" x="669" y="340" width="14" height="14" rx="2"/>
  <rect class="d-box d-role-embedding" x="669" y="360" width="14" height="14" rx="2"/>
  <rect class="d-box d-role-embedding" x="669" y="380" width="14" height="14" rx="2"/>
  <text class="d-title" x="676" y="411" text-anchor="middle">⋮</text>
  <rect class="d-box d-role-embedding" x="669" y="420" width="14" height="14" rx="2"/>
  <text class="d-note" x="676" y="452" text-anchor="middle">patch outputs</text>
  <text class="d-note" x="676" y="465" text-anchor="middle">unused</text>
  <path class="d-line-accent" d="M683 347 H722" marker-end="url(#vit-arrow-accent)"/>
  <rect class="d-box d-role-output" x="724" y="332" width="150" height="46" rx="5"/>
  <text class="d-title" x="799" y="351" text-anchor="middle">LayerNorm → MLP head</text>
  <text class="d-note" x="799" y="367" text-anchor="middle">reads only z_L⁰ ([class])</text>
  <path class="d-line-strong" d="M799 378 V406" marker-end="url(#vit-arrow)"/>
  <text class="d-title" x="799" y="424" text-anchor="middle">class</text>
  <text class="d-note" x="799" y="440" text-anchor="middle">head: 1 hidden layer (pre-train),</text>
  <text class="d-note" x="799" y="453" text-anchor="middle">1 linear layer (fine-tune)</text>
  </svg>
diagram_caption: "ViT cuts the image into 16×16 patches, projects each to a token, prepends a [class] token and adds position embeddings; L pre-norm encoder blocks (attention, MLP, residual adds) follow, and a head reads the final [class] token."
notable_models:
  - { name: "ViT-B/L/H", year: 2020, note: "The original: a pure transformer that, pretrained on ImageNet-21k or JFT-300M, approaches or beats the state of the art on image recognition benchmarks." }
  - { name: "DeiT", year: 2021, note: "A convolution-free transformer trained on ImageNet only, with a distillation token for teacher-student training." }
  - { name: "Swin Transformer", year: 2021, note: "Hierarchical transformer that limits self-attention to shifted local windows, with linear cost in image size; a general-purpose backbone for detection and segmentation." }
  - { name: "DINOv2", year: 2023, note: "Self-supervised pretraining of a 1B-parameter ViT, distilled into smaller models, producing visual features that work without fine-tuning." }
  - { name: "SAM (Segment Anything)", year: 2023, note: "Promptable segmentation model whose image encoder is an MAE-pretrained ViT; transfers zero-shot to new image distributions and tasks." }

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
updated: '2026-11-21'
sources:
  - { url: "https://arxiv.org/abs/2010.11929", title: "An Image is Worth 16x16 Words (Dosovitskiy et al., 2020)", trust: 0.95 }
  - { url: "https://huggingface.co/docs/transformers/model_doc/vit", title: "Vision Transformer (ViT) — Hugging Face documentation", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2012.12877", title: "Training data-efficient image transformers & distillation through attention (Touvron et al., 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2103.14030", title: "Swin Transformer: Hierarchical Vision Transformer using Shifted Windows (Liu et al., 2021)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2304.07193", title: "DINOv2: Learning Robust Visual Features without Supervision (Oquab et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2304.02643", title: "Segment Anything (Kirillov et al., 2023)", trust: 0.95 }
---

## What Changed
The Vision Transformer (Dosovitskiy et al., 2020) **removes the reliance on convolutional networks**.
It cuts an image into fixed patches (for example 16×16 pixels), linearly projects each patch into a
token, adds a position embedding, and feeds the sequence into a standard transformer encoder.

- **Small data**: trained on ImageNet alone without strong regularization, it is a few points below
  comparable ResNets.
- **Large data**: pretrained on 14M–300M images, it **approaches or beats** the state of the art on
  multiple image recognition benchmarks, per the authors.

## Key Ideas
- **Patch tokenization**: an image becomes a short sequence of patch embeddings (196 for a 224×224
  image with 16×16 patches), instead of one token per pixel.
- **Less inductive bias**: no convolutions in the encoder; locality and translation behaviour must be
  learned from data.
- **Data-size crossover**: ResNets do better on mid-sized datasets; ViT does better once pretraining
  data is large.
