---
id: architecture-era/cnn
type: architecture-era
slug: cnn
schema_version: 1.0
title: "Convolutional Neural Networks"
name: "Convolutional Neural Network"
era: foundation
era_range: "1989–2015"
year: 2012
family: vision
core_innovation: "Weight-shared convolutional filters plus pooling exploit spatial locality, cutting parameters and building translation-tolerant features."
description: "From LeNet to AlexNet — the architecture that made GPU-trained deep vision work at scale."
key_features: ["Weight-shared convolution kernels", "Local receptive fields", "Pooling for translation tolerance", "Hierarchical feature learning", "GPU-scale training"]
key_models: ["LeNet-5 (1998)", "AlexNet (2012)", "VGG (2014)", "GoogLeNet (2014)"]
paper_title: "ImageNet Classification with Deep Convolutional Neural Networks (AlexNet)"
paper_url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html"
influenced_by: [architecture-era/mlp-backprop]
led_to: [architecture-era/resnet, architecture-era/vision-transformer]

eli5: "A convolutional neural network looks at a picture through a tiny sliding window, hunting for one simple pattern — an edge, a corner, a patch of colour. It slides that same window over the whole image, so it can spot the pattern anywhere. Stack these searches and simple patterns combine into edges, then shapes, then whole objects like a cat or a car."

analogy:
  title: "A rubber stamp that hunts for one pattern"
  body: "Imagine a small rubber stamp cut to detect one thing — say, a vertical edge. You press it at the top-left of a photo: bright ink where an edge is, faint where none. Then you slide it one step right and stamp again, marching across the whole image. The sheet of stamps you produce is a map of where that edge appears — a 'feature map'. Crucially it is the *same* stamp everywhere, so an edge in the corner and an edge in the centre light up identically. A convolutional network is a stack of thousands of learned stamps: the first layer stamps for edges and colours, and later layers stamp for combinations of the earlier maps — eyes, wheels, faces."

narrative:
  motivation: "By the late 1980s neural networks could classify small images, but only as flat vectors: every pixel wired to every hidden unit. A modest 200×200 image already needs tens of thousands of weights per neuron, so parameters exploded, training overfit, and — worst of all — the network had to relearn the same pattern separately in every location. A cat in the top-left and a cat in the bottom-right looked like two unrelated problems."
  approach: "The insight, from LeCun's work on handwritten digits, was to bake the geometry of images into the architecture. Vision has two facts a dense network ignores: patterns are *local* (a pixel relates most strongly to its neighbours) and *position-independent* (an edge is an edge wherever it sits). So use a small filter that looks at a local patch, and slide that *same* filter across the whole image."
  solution: "This gives three ingredients. Convolution: a small kernel of shared weights sweeps the image, producing a feature map of where its pattern occurs. Nonlinearity (ReLU): keep the strong responses, zero the rest. Pooling: downsample each map so the network becomes tolerant to small shifts and later layers see a wider field of view. Stack these blocks and features compose — edges into textures, textures into parts, parts into objects."
  outcome: "LeNet-5 proved the idea on cheques and ZIP codes in the 1990s, but compute held it back. In 2012 AlexNet — a deep convolutional network with ReLU, dropout, data augmentation, and two GPUs — nearly halved the error rate on the ImageNet benchmark overnight. That single result ended the era of hand-designed image features and launched modern deep learning. Every later vision model, from ResNet to the Vision Transformer, is a descendant of this moment."

build_steps:
  - { title: "1. Start with the raw pixels", detail: "An image is a grid of numbers: height × width × 3 colour channels (red, green, blue). This grid, not a flattened vector, is what the network reads — the spatial layout is kept intact." }
  - { title: "2. Convolve with learned filters", detail: "Slide each small filter (e.g. 3×3) across the image, computing a weighted sum of the patch under it at every position. Each filter outputs one feature map; a layer has many filters, so it outputs a stack of maps." }
  - { title: "3. Apply a nonlinearity (ReLU)", detail: "Pass every value through ReLU — keep it if positive, set it to zero if negative. Without this the whole stack would collapse into one linear filter and could not learn curved, complex patterns." }
  - { title: "4. Pool to shrink and stabilise", detail: "Downsample each map (e.g. take the maximum in each 2×2 block). This halves the resolution, makes the features tolerant to small shifts, and lets deeper layers cover more of the image." }
  - { title: "5. Stack blocks to build a hierarchy", detail: "Repeat convolve → ReLU → pool many times. Early layers detect edges and colour blobs; middle layers detect textures and parts; deep layers respond to whole objects. Channels grow as spatial size shrinks." }
  - { title: "6. Flatten and classify", detail: "After the final block, flatten the small, deep feature maps into a vector and feed it to a few fully connected layers plus a softmax that outputs a probability for each class." }

key_equation:
  latex: "S(i,j)=\\sum_{m}\\sum_{n} I(i+m,\\, j+n)\\,K(m,n)"
  plain: "To get the feature-map value at position (i, j), lay the small filter K over the patch of the image starting at (i, j), multiply each filter weight by the pixel it covers, and add them all up. Slide the filter to the next position and repeat. Because the same K is used everywhere, the network detects its pattern wherever it appears."
  terms:
    - { symbol: "S(i,j)", meaning: "the output feature map — how strongly the filter's pattern is present at position (i, j)" }
    - { symbol: "I", meaning: "the input image (or the previous layer's feature map)" }
    - { symbol: "K(m,n)", meaning: "the learned filter/kernel of shared weights, indexed over its small window" }
    - { symbol: "m, n", meaning: "offsets that sweep across the filter's window (e.g. 0–2 for a 3×3 kernel)" }

strengths:
  - "Weight sharing means a pattern is learned once and detected everywhere, so far fewer parameters than a dense network on the same image."
  - "Built-in translation tolerance — an object is recognised no matter where it sits in the frame."
  - "Learns a natural hierarchy: edges → textures → parts → objects, with no hand-engineered features."
  - "Strong results even on modest datasets, because the spatial prior does a lot of the work."

weaknesses:
  - "The receptive field grows slowly with depth, so capturing very long-range or global relationships needs many layers."
  - "Baked-in locality is a hard prior — helpful on images, but limiting where relationships are not spatial."
  - "Pooling and strided convolution discard precise spatial detail, which hurts tasks needing exact localisation."
  - "Fixed grid structure makes it awkward for irregular data (graphs, point clouds, variable-length sequences)."

diagram: |
  flowchart LR
    IMG["Input image (H x W x 3)"] --> C1["Conv filters + ReLU"]
    C1 --> P1["Pooling (downsample)"]
    P1 --> C2["Conv filters + ReLU"]
    C2 --> P2["Pooling (downsample)"]
    P2 --> F["Flatten"]
    F --> FC["Fully connected + Softmax"]
    FC --> OUT["Class probabilities"]
diagram_caption: "A convolutional network alternates convolution and pooling to build ever-higher-level features, then a fully connected head turns them into class scores."

notable_models:
  - { name: "LeNet-5", year: 1998, note: "Proved convolution + pooling could read handwritten digits reliably — the blueprint for every CNN that followed." }
  - { name: "AlexNet", year: 2012, note: "Showed a deep CNN with ReLU, dropout, and GPU training could crush the ImageNet benchmark — the spark of the deep learning boom." }
  - { name: "VGG", year: 2014, note: "Proved that stacking many small 3×3 filters into a deep, uniform network keeps improving accuracy." }
  - { name: "GoogLeNet / Inception", year: 2014, note: "Showed multi-scale filters in parallel (the Inception block) give accuracy at a fraction of the compute." }

resources:
  - { kind: course, title: "CS231n: Convolutional Neural Networks for Visual Recognition (Stanford)", url: "https://cs231n.github.io/convolutional-networks/" }
  - { kind: blog, title: "Conv Nets: A Modular Perspective (Chris Olah)", url: "https://colah.github.io/posts/2014-07-Conv-Nets-Modular/" }
  - { kind: video, title: "But what is a convolution? (3Blue1Brown)", url: "https://www.youtube.com/watch?v=KuXjwB4LzSA" }
  - { kind: paper, title: "ImageNet Classification with Deep CNNs / AlexNet (Krizhevsky et al., 2012)", url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html" }
  - { kind: paper, title: "Gradient-Based Learning Applied to Document Recognition / LeNet (LeCun et al., 1998)", url: "https://doi.org/10.1109/5.726791" }

learn_first:
  - { label: "The multilayer perceptron & backprop", href: "/models/architecture/mlp-backprop", tag: "Architecture" }
  - { label: "What a neural network is", href: "/learn/concepts/neural-network", tag: "Concept" }
  - { label: "ReLU & activation functions", href: "/learn/concepts/activation-functions", tag: "Math" }

tags: [cnn, vision, convolution, alexnet, foundational]
updated: 2026-07-20
sources:
  - { url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html", title: "ImageNet Classification with Deep Convolutional Neural Networks (Krizhevsky, Sutskever & Hinton, NeurIPS 2012)", trust: 0.95 }
  - { url: "https://doi.org/10.1109/5.726791", title: "Gradient-Based Learning Applied to Document Recognition (LeCun et al., 1998)", trust: 0.9 }
  - { url: "https://cs231n.github.io/convolutional-networks/", title: "CS231n Convolutional Networks (Stanford)", trust: 0.9 }
---

## What Changed
A fully connected network treats an image as an unstructured vector: every pixel connects to every
hidden unit, so parameters explode and the model must relearn a pattern separately in every location.
The convolutional network bakes in the geometry of images. A small kernel slides across the image,
sharing the *same* weights at every position — this is a strong prior (features are useful wherever
they appear) that slashes parameter count and gives translation tolerance. Stacking convolution and
pooling builds a hierarchy: edges → textures → parts → objects. LeCun's LeNet proved the idea on
digits in the 1990s, but it was AlexNet in 2012 — deep, ReLU-activated, dropout-regularised, and
trained on two graphics processing units (GPUs) — that halved the ImageNet error rate overnight and
triggered the modern deep learning boom. It descends directly from the backprop-trained multilayer
perceptron and set up both very deep residual nets and, eventually, the patch-based Vision Transformer.

## Key Ideas
- **Weight sharing** — one kernel is reused across all spatial positions, so the model learns a
  feature once and detects it everywhere, with far fewer parameters than a dense layer.
- **Local receptive fields** — each unit sees a small neighbourhood; depth grows the effective
  receptive field so deeper layers integrate wider context.
- **Pooling** — downsampling (max/average) adds translation tolerance and shrinks spatial resolution
  while growing channel depth.
- **Hierarchical features** — early layers learn edges and colour blobs, later layers learn object
  parts, an emergent compositional representation.
- **Scale + hardware** — ReLU, dropout, data augmentation, and GPU training (AlexNet) were the
  practical unlocks that turned a decades-old idea into a breakthrough.
