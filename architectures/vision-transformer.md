---
id: architecture-era/vision-transformer
type: architecture-era
slug: vision-transformer
schema_version: 3.1
title: Vision Transformer (ViT)
name: Vision Transformer
era: scaling
era_range: 2020–2023
year: 2020
family: vision
core_innovation: Treat an image as a sequence of patch embeddings and run a standard Transformer encoder over them, with no convolutions in the model.
description: A neural network for images that cuts a picture into small square patches, treats each patch like a word, and runs the same Transformer encoder used for text over the sequence of patches.
key_features:
- Image split into fixed-size patches
- Linear patch embeddings plus learned position embeddings
- A learnable [class] token whose final state classifies the image
- Standard Transformer encoder over the patch tokens
- Two-layer MLP with GELU as the feed-forward sub-layer of each block
paper_title: 'An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale'
paper_url: https://arxiv.org/abs/2010.11929
influenced_by:
- architecture-era/transformer
- architecture-era/cnn
led_to: []
analogy:
  title: A mosaic where every tile can look at every other tile
  body: This is an analogy, not the mechanism. Picture a photo cut into square tiles, like a mosaic, and each tile handed a short written description of itself. In a convolutional network each tile first compares notes only with its close neighbours, and news from the far corner arrives layer by layer. In the Vision Transformer every tile reads every other tile's description in the same step and decides how much to borrow from each. That all-to-all reading is self-attention, the same rule the Transformer uses for words; the authors report that the trained model attends to the image regions that matter for the class.
narrative:
  motivation: '**Vision still ran on convolutions.** The paper is by **Dosovitskiy et al. (2020)** at Google Research.


    - **Language:** the authors write that the Transformer had become the **de facto standard** for natural language processing.

    - **Vision:** attention was used **together with** convolutional networks, or to replace parts of them while keeping their overall structure.

    - **The question:** whether a **pure Transformer**, with none of a convolutional network''s built-in image assumptions, can classify images well.'
  approach: '**Use patches, not pixels, as the tokens.**


    - **The obstacle:** attention compares every element with every other, so attending pixel by pixel has a cost **quadratic in the number of pixels**, which the authors say does not scale to realistic input sizes.

    - **The fix:** cut the image into fixed-size patches, for example **16 × 16 pixels**, and treat each patch the way a Transformer treats a word.

    - **Fewest changes:** the authors follow the original Transformer **as closely as possible**, so existing implementations can be used almost out of the box.'
  solution: '**A standard Transformer encoder over a sequence of patch embeddings.**


    - **Patch embedding:** each patch is flattened and multiplied by one shared, trainable matrix **E**, giving a vector of **D** numbers (D = **768** in ViT-Base).

    - **Class token:** a learnable **[class]** embedding is placed in front of the patches; its final state is the image representation.

    - **Position:** learned **1-D position embeddings** are added; 2-D-aware versions gave no significant gain in the authors'' tests.

    - **Encoder:** alternating multi-head self-attention and **two-layer MLP** blocks, with LayerNorm **before** each block and a residual connection after it.'
  outcome: '**Scale decides the result**, in the authors'' words: large-scale training trumps inductive bias.


    - **ImageNet alone (1.3M images):** without strong regularization, accuracies **a few percentage points below** ResNets of comparable size.

    - **14M to 300M images:** pre-trained on ImageNet-21k or JFT-300M, ViT **approaches or beats** the state of the art on several benchmarks.

    - **Best model:** **ViT-H/14** reaches **88.55%** on ImageNet, **94.55%** on CIFAR-100 and **77.63%** on the 19-task VTAB suite.

    - **Compute:** pre-training ViT-L/16 on JFT-300M took **0.68k TPUv3-core-days** (Google tensor processing unit cores times days), against **9.9k** for the BiT-L ResNet pre-trained on the same data (ImageNet: **87.76%** against **87.54%**).'
key_equation:
  latex: \mathbf{z}_0 = [\,\mathbf{x}_{\text{class}};\ \mathbf{x}_p^1 \mathbf{E};\ \mathbf{x}_p^2 \mathbf{E};\ \dots;\ \mathbf{x}_p^N \mathbf{E}\,] + \mathbf{E}_{\text{pos}}
  plain: 'In words: multiply each of the N flattened patches by the same matrix E to get one token per patch, put the learnable class token in front, and add a position embedding to every slot. The result z₀ is the encoder''s input: N + 1 tokens of D numbers each. The worked example above builds z₀ by hand for a 4 × 4 image (Eq. 1 of the paper).'
  terms:
  - symbol: z₀
    meaning: 'the sequence handed to the Transformer encoder: N + 1 rows of D numbers (197 rows of 768 for ViT-Base/16 at 224 × 224)'
  - symbol: x_pⁱ
    meaning: the i-th patch, its P × P × C pixel values written out as one row (16 × 16 × 3 = 768 values for an RGB 16 × 16 patch)
  - symbol: E
    meaning: the trainable patch-embedding matrix, (P²·C) rows by D columns, shared by every patch
  - symbol: x_class
    meaning: the learnable [class] embedding put in front; its state after the last layer, after a LayerNorm, is the image representation y
  - symbol: E_pos
    meaning: the learned position embeddings, (N + 1) rows by D columns, one row added to each slot
  - symbol: N
    meaning: the number of patches, H·W / P² for an H × W image and P × P patches
strengths:
- '**Global from the first layer:** the self-attention layers are global; the authors find that some heads attend to **most of the image already in the lowest layers**.'
- '**Less pre-training compute:** in the authors'' scaling study ViT needs about **2 to 4 times less** compute than ResNets to reach the same performance (average over 5 datasets).'
- '**Gains with scale:** larger ViT variants overtake smaller ones as the dataset grows, and the **full benefit of larger models** appears only with **JFT-300M**; the authors saw no saturation within the range they tried.'
- '**Reuses language-model code:** the authors note that scalable natural language processing (NLP) Transformer architectures and their efficient implementations can be used **almost out of the box**.'
- '**Memory:** the authors report that large ViT models fit **larger batches per accelerator core** than ResNet models (Appendix D.5).'
weaknesses:
- '**Needs a lot of data:** trained on mid-sized datasets such as ImageNet without strong regularization, ViT is **a few percentage points below** ResNets of comparable size.'
- '**Few built-in image assumptions:** only the MLP layers are local; the position embeddings start with **no information about 2-D position**, so all spatial relations between patches must be **learned from data**.'
- '**Cost grows with resolution:** the sequence length is inversely proportional to the **square of the patch size**, so smaller patches cost more; the Swin Transformer authors describe ViT''s self-attention cost as **quadratic in image size**.'
- '**One coarse resolution:** ViT keeps a **single, low-resolution** grid of patch tokens through every layer; the Swin Transformer authors judge it unsuitable as a general-purpose backbone for **dense vision tasks** such as segmentation, or for high-resolution input, for that reason and its cost.'
- '**Self-supervision lagged:** the paper''s masked-patch pre-training gave ViT-B/16 **79.9%** on ImageNet, **4% behind** supervised pre-training.'
visual:
  kind: distribution
  tokens:
  - '[class]'
  - patch 1
  - patch 2
  - patch 3
  - patch 4
  logits:
  - 1
  - 3
  - 0
  - 1
  - 3
  temperatures:
  - 1
  - 1.4142
  panel_titles:
  - Raw scores 1, 3, 0, 1, 3
  - Scores divided by √2
  caption: 'The attention weights of the [class] token over the five tokens of the worked example, computed from its scores 1, 3, 0, 1 and 3. Left: softmax of the raw scores. Right: the scores divided by √D_h = √2 ≈ 1.414 first, as ViT''s self-attention does. The numbers are an example, not from a trained model.'
comparison:
  before: Convolutional network (ResNet)
  after: Vision Transformer
  rows:
  - aspect: Image assumptions built into the layers
    before: Locality, 2-D neighbourhood structure and translation equivariance in every layer
    after: Only the MLP layers are local; self-attention is global, and 2-D structure is used only to cut patches and to resize position embeddings
    source: https://arxiv.org/abs/2010.11929
  - aspect: Pre-trained on ImageNet alone (1.3M images)
    before: 'Ahead: the BiT ResNets outperform ViT on ImageNet (Figure 3)'
    after: A few percentage points below ResNets of comparable size, without strong regularization
    source: https://arxiv.org/abs/2010.11929
  - aspect: ImageNet top-1 after pre-training on JFT-300M (Table 2)
    before: 87.54% (BiT-L, ResNet152x4)
    after: 87.76% (ViT-L/16); 88.55% (ViT-H/14)
    source: https://arxiv.org/abs/2010.11929
  - aspect: Pre-training compute for those models (TPUv3-core-days)
    before: 9.9k
    after: 0.68k (ViT-L/16); 2.5k (ViT-H/14)
    source: https://arxiv.org/abs/2010.11929
  - aspect: Compute to reach the same transfer performance (scaling study)
    before: The baseline
    after: About 2 to 4 times less (average over 5 datasets)
    source: https://arxiv.org/abs/2010.11929
key_numbers:
  columns:
  - Setting
  - ViT-Base
  - ViT-Large
  - ViT-Huge
  rows:
  - cells:
    - Encoder layers (L)
    - '12'
    - '24'
    - '32'
    source: https://arxiv.org/abs/2010.11929
  - cells:
    - Numbers in each token's vector (hidden size D)
    - '768'
    - '1024'
    - '1280'
    source: https://arxiv.org/abs/2010.11929
  - cells:
    - Inner width of the MLP
    - '3072'
    - '4096'
    - '5120'
    source: https://arxiv.org/abs/2010.11929
  - cells:
    - Attention heads
    - '12'
    - '16'
    - '16'
    source: https://arxiv.org/abs/2010.11929
  - cells:
    - Width of each head, D ÷ heads (the paper sets D_h = D / k)
    - '64'
    - '64'
    - '80'
    source: https://arxiv.org/abs/2010.11929
  - cells:
    - Parameters
    - 86 million
    - 307 million
    - 632 million
    source: https://arxiv.org/abs/2010.11929
  - cells:
    - Patch sizes in the paper's results
    - 16 and 32
    - 16 and 32
    - '14'
    source: https://arxiv.org/abs/2010.11929
  - cells:
    - Tokens for a 224 × 224 image (N = 224² / P², plus the class token)
    - 197 at P = 16
    - 197 at P = 16
    - 257 at P = 14
    source: https://arxiv.org/abs/2010.11929
  - cells:
    - ImageNet top-1 after JFT-300M pre-training, fine-tuned at 384 (Table 5)
    - 84.15% (B/16)
    - 87.12% (L/16)
    - 88.04% (H/14)
    source: https://arxiv.org/abs/2010.11929
  - cells:
    - Pre-training batch size (all models)
    - '4096'
    - '4096'
    - '4096'
    source: https://arxiv.org/abs/2010.11929
misunderstandings:
- misunderstanding: ViT treats every pixel as a token.
  truth: Each token is a **patch**, for example 16 × 16 pixels. A 224 × 224 image becomes **196** patch tokens plus one class token. The authors point out that attending pixel by pixel would cost **quadratically in the number of pixels**, which does not scale to realistic image sizes.
  source: https://arxiv.org/abs/2010.11929
- misunderstanding: ViT beats convolutional networks whatever the amount of training data.
  truth: Pre-trained on ImageNet alone, ViT is **a few percentage points below** comparable ResNets. ViT-B/16 reaches **77.91%** on ImageNet when pre-trained on ImageNet and **84.15%** when pre-trained on JFT-300M (Table 5). The authors conclude that the convolutional inductive bias helps on **smaller datasets**, while large datasets let ViT learn the patterns directly.
  source: https://arxiv.org/abs/2010.11929
- misunderstanding: ViT needs position embeddings that encode the 2-D grid.
  truth: The paper uses plain **learned 1-D** position embeddings, one per slot in raster order. In its ablation (Table 8, ViT-B/16, ImageNet 5-shot linear) **no** position embedding scored **0.614**, 1-D **0.642** and 2-D **0.640**. The learned embeddings come to reflect rows and columns on their own (Figure 7).
  source: https://arxiv.org/abs/2010.11929
- misunderstanding: The [class] token is essential to make ViT work.
  truth: The authors kept the [class] token to stay close to the original Transformer. Averaging the patch outputs instead (global average pooling) worked **similarly well** once the **learning rate** was changed (Appendix D.3, Figure 9).
  source: https://arxiv.org/abs/2010.11929
diagram_svg: '<svg class="atlas-figure" viewBox="0 0 900 520" role="img" aria-label="Vision Transformer: an image is cut into 16 by 16 patches, each patch is flattened and linearly projected into a token, a learnable class token is prepended and position embeddings are added; the sequence passes through L pre-norm encoder blocks of LayerNorm, multi-head self-attention, residual add, LayerNorm, MLP and residual add, and an MLP head reads the final class token to predict the class.">

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

  '
diagram_caption: ViT cuts the image into 16×16 patches, projects each to a token, prepends a [class] token and adds position embeddings; L pre-norm encoder blocks (attention, MLP, residual adds) follow, and a head reads the final [class] token.
underlying_model_note: 'The first figure is the Vision Transformer as its paper describes it. The model drawn below, google/vit-base-patch16-224, is a ViT-Base with 16 × 16 patches: 12 pre-norm encoder blocks, vectors of 768 numbers, 12 heads and an MLP of 3072, pre-trained on ImageNet-21k and fine-tuned on ImageNet at 224 × 224.'
notable_models:
- name: ViT-B/L/H
  year: 2020
  note: 'The original: pre-trained on ImageNet-21k or JFT-300M, it approaches or beats the state of the art on several image benchmarks; ViT-H/14 reaches 88.55% on ImageNet.'
- name: DeiT
  year: 2020
  note: A convolution-free Transformer trained on ImageNet only, on a single computer in under 3 days; its 86M-parameter reference model reaches 83.1% top-1 with no external data. Adds a distillation token for learning from a teacher network.
- name: Swin Transformer
  year: 2021
  note: A hierarchical Transformer that limits self-attention to shifted local windows, giving a cost linear in image size; built as a general-purpose backbone for classification, detection and segmentation.
- name: DINOv2
  year: 2023
  note: Self-supervised pre-training of a ViT with about 1 billion parameters, distilled into smaller ViTs; its features can be used without fine-tuning.
- name: SAM (Segment Anything)
  year: 2023
  note: A promptable segmentation model whose image encoder is a ViT-H pre-trained as a masked autoencoder (MAE); its authors report zero-shot transfer to new image distributions and tasks.
resources:
- kind: paper
  title: An Image is Worth 16x16 Words (Dosovitskiy et al., 2020)
  url: https://arxiv.org/abs/2010.11929
- kind: blog
  title: The Illustrated Transformer (Jay Alammar) — the engine ViT reuses
  url: https://jalammar.github.io/illustrated-transformer/
- kind: tutorial
  title: Vision Transformer (ViT) — Hugging Face docs
  url: https://huggingface.co/docs/transformers/model_doc/vit
- kind: video
  title: An Image is Worth 16x16 Words — ViT explained (Yannic Kilcher)
  url: https://www.youtube.com/watch?v=TrdevFK_am4
learn_first:
- label: The Transformer & self-attention
  id: architecture-era/transformer
  tag: Architecture
- label: Convolutional neural networks (what ViT replaces)
  id: architecture-era/cnn
  tag: Architecture
- label: Embeddings & token representations
  tag: Concept
tags:
- vit
- vision
- transformer
- patches
- scaling
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/2010.11929
  title: 'An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale (Dosovitskiy et al., 2020)'
  trust: 0.98
- url: https://huggingface.co/google/vit-base-patch16-224
  title: google/vit-base-patch16-224 — model card (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/vit-base-patch16-224/blob/main/config.json
  title: google/vit-base-patch16-224 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2012.12877
  title: Training data-efficient image transformers & distillation through attention (Touvron et al., 2020)
  trust: 0.95
- url: https://arxiv.org/abs/2103.14030
  title: 'Swin Transformer: Hierarchical Vision Transformer using Shifted Windows (Liu et al., 2021)'
  trust: 0.95
- url: https://arxiv.org/abs/2304.07193
  title: 'DINOv2: Learning Robust Visual Features without Supervision (Oquab et al., 2023)'
  trust: 0.95
- url: https://arxiv.org/abs/2304.02643
  title: Segment Anything (Kirillov et al., 2023)
  trust: 0.95
---

## What Changed

Before ViT, the authors write, **ResNet-like convolutional networks** were still the state of the
art in large-scale image recognition. A convolution looks at a small window of pixels at a time, so
locality is built into every layer.

How to read the table above:

- **Inductive bias** means an assumption built into the model before it sees any data. A
  convolutional network assumes nearby pixels belong together; ViT assumes almost nothing.
- **ResNet and BiT:** ResNet is a widely used convolutional network; BiT ("Big Transfer") is the
  paper's ResNet baseline, pre-trained on the same data as ViT.
- **JFT-300M** is Google's in-house set of about 300 million images; **ImageNet** has 1.3 million.
- **TPUv3-core-days** count the accelerator cores used for pre-training times the days of training.
- **The trade:** with little data the built-in assumptions help the ResNet; with a lot of data ViT
  learns them itself and needs less compute for the same accuracy.

## Key Ideas

- **A patch is a word.** An image becomes a short sequence of patch tokens, 196 for a 224 × 224
  image with 16 × 16 patches, instead of one token per pixel.
- **Change as little as possible.** The encoder follows the original Transformer as closely as
  possible; LayerNorm is applied before each block, and the residual connection after it.
- **Position is learned.** Plain 1-D learned position embeddings work as well as 2-D-aware ones.
- **Data size decides.** ResNets do better on mid-sized datasets; ViT does better once
  pre-training data is large.

## How It Works

The figure above runs left to right along the top, then through the encoder block below. Follow
it with a 224 × 224 colour image and ViT-Base/16, the settings the figure is labelled with.

A **vector** is a list of numbers. A **token** is one item of the sequence the Transformer reads;
here each token is one patch, carried as a vector of **D = 768** numbers.

1. **Image to patches.** The image is cut into a grid of 16 × 16-pixel squares: 224 / 16 = 14
   per side, so **14 × 14 = 196 patches**. The paper writes N = HW / P² for an H × W image and
   P × P patches.
2. **Flatten each patch.** A patch holds 16 × 16 pixels with 3 colour values each, so it is
   written out as one row of **768 numbers**.
3. **Linear projection E.** Every patch row is multiplied by the same trainable matrix E, which
   turns it into a vector of D numbers (768 for Base). The outputs are the **patch embeddings**.
4. **The [class] token.** One extra learnable vector is put in front of the 196 patch embeddings,
   like BERT's [class] token. After the last layer, its state is used as the summary of the whole
   image.
5. **Add position embeddings.** A learned vector is added to each of the **197 slots**, so the
   model can tell where each patch came from. The result is z₀ (the key equation below).
6. **Encoder block, first half.** LayerNorm (layer normalisation, which rescales each token's
   vector), then **multi-head self-attention** (MSA): every token builds a query, a
   key and a value and takes a weighted blend of all 197 values. ViT-Base runs **12 heads**, each
   with vectors of 768 / 12 = **64** numbers. The block's input is then added back (the residual
   connection).
7. **Encoder block, second half.** LayerNorm again, then an **MLP** (multilayer perceptron, here
   two layers) applied to each token on its own: 768 numbers in, 3072 in the middle with a GELU
   (Gaussian error linear unit) activation, 768 out. A second residual
   addition follows.
8. **Repeat L times.** ViT-Base stacks **12** such blocks, ViT-Large 24, ViT-Huge 32. The token
   count stays 197 and the width stays 768 throughout.
9. **Read the class.** Only the final [class] token is used: a LayerNorm, then the classification
   head. During pre-training the head is an MLP with **one hidden layer** (tanh); for fine-tuning
   it is replaced by a **single linear layer**, zero-initialised, with one output per class.
10. **Fine-tuning at higher resolution.** The patch size stays the same, so a larger image gives a
    longer sequence (a 384 × 384 image gives 24 × 24 = 576 patches). The learned position
    embeddings are **interpolated in 2-D** to fit the new grid.

## Worked Example

**From pixels to attention weights by hand**, for a 4 × 4 grey image with 2 × 2 patches and
vectors of D = 2 numbers. The numbers are an example, not from a trained model; a real ViT-Base
uses 16 × 16 × 3 patches and D = 768.

**Step 0, the real sizes:** a 224 × 224 image with 16 × 16 patches gives 196 patches plus the class
token, so 197 tokens and a 197 × 197 table of attention scores (38,809 scores) per head. One token
per pixel would mean 50,176 tokens and about 2.5 billion scores.

The image, one grey value per pixel:

| | col 1 | col 2 | col 3 | col 4 |
| --- | --- | --- | --- | --- |
| row 1 | 2 | 0 | 0 | 0 |
| row 2 | 0 | 2 | 0 | 0 |
| row 3 | 0 | 0 | 3 | 3 |
| row 4 | 0 | 0 | 3 | 3 |

1. **Cut into four 2 × 2 patches and flatten each** (top-left, top-right, bottom-left,
   bottom-right; each read row by row):
   - patch 1 = (2, 0, 0, 2); patch 2 = (0, 0, 0, 0); patch 3 = (0, 0, 0, 0); patch 4 = (3, 3, 3, 3).
2. **Project with E.** Take E with rows (0.5, 0), (0, 0.5), (0, 0.5), (0.5, 0). Multiplying a
   patch (a, b, c, d) by E gives (0.5a + 0.5d, 0.5b + 0.5c):
   - patch 1 → (0.5 × 2 + 0.5 × 2, 0) = **(2, 0)**
   - patch 2 → **(0, 0)**; patch 3 → **(0, 0)**
   - patch 4 → (0.5 × 3 + 0.5 × 3, 0.5 × 3 + 0.5 × 3) = **(3, 3)**
3. **Put the class token in front.** Take x_class = **(1, 0)**.
4. **Add position embeddings** (0, 0), (1, 0), (0, 1), (1, 1), (0, 0) to the five slots:
   - [class]: (1, 0) + (0, 0) = **(1, 0)**
   - patch 1: (2, 0) + (1, 0) = **(3, 0)**
   - patch 2: (0, 0) + (0, 1) = **(0, 1)**
   - patch 3: (0, 0) + (1, 1) = **(1, 1)**
   - patch 4: (3, 3) + (0, 0) = **(3, 3)**
   - Patches 2 and 3 were both blank, and now differ: only the position tells them apart.
5. **Score the [class] token against every token.** To keep the arithmetic short, take the
   query, key and value of each token to be its vector itself and skip the LayerNorm (a real model
   learns three separate projections). Dot products with the query (1, 0):
   - [class] 1 × 1 + 0 × 0 = **1**; patch 1 **3**; patch 2 **0**; patch 3 **1**; patch 4 **3**.
6. **Scale and softmax.** Divide by √D_h = √2 ≈ 1.414: the scores become 0.707, 2.121, 0, 0.707,
   2.121.
   - e to each: 2.028, 8.342, 1.000, 2.028, 8.342; the total is 21.741.
   - Weights: **0.093, 0.384, 0.046, 0.093, 0.384**. They add up to 1.
7. **Blend the values** with those weights:
   - 0.093 × (1, 0) = (0.093, 0); 0.384 × (3, 0) = (1.152, 0); 0.046 × (0, 1) = (0, 0.046)
   - 0.093 × (1, 1) = (0.093, 0.093); 0.384 × (3, 3) = (1.152, 1.152)
   - Sum: **(2.49, 1.29)**, the [class] token's new vector after this attention step.

**What the figure shows:** the [class] token's weights without and with the division by √D_h.
Without it, softmax(1, 3, 0, 1, 3) gives 0.058, 0.431, 0.021, 0.058 and 0.431; with it, the
weights above, which are more even.

**What this means:** the [class] token gathers most of its new vector from the two patches whose
vectors best match its query, patch 1 and patch 4, and a little from the rest. Repeated in 12 heads
and 12 layers, this is how one token comes to summarise the whole image for the classification
head.
