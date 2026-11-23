---
id: parameter/cnn-dilation
type: parameter
slug: cnn-dilation
schema_version: 3.1
title: Dilation (Atrous Convolution)
name: dilation
priority: 7
importance: optional
applies_to:
- cnn
range: 1 — 24
controls: 'How far apart the filter''s taps are spread: at dilation d, neighbouring weights read cells d apart, so the same filter covers a wider patch.'
effects:
  decrease:
  - 'A narrower span: a k × k filter covers d × (k − 1) + 1 cells per side'
  - 'At dilation 1 the taps are side by side: an ordinary convolution'
  - No change in parameters
  increase:
  - 'A wider span for the same weights: a 3 × 3 filter at dilation 2 spans 5 × 5'
  - 'Stacked, the view grows fast: Yu and Koltun''s dilations 1, 1, 2, 4, 8, 16 reach 65 × 65 cells'
  - 'Equal dilations in a row read a checkerboard: at k = 3, d = 2 only 9 of 25 cells (Wang et al.)'
  - A smaller output unless the padding grows with it
scale:
  min: 1
  max: 32
  typical_min: 1
  typical_max: 24
  spacing: log
typical_values:
- setting: PyTorch Conv2d default, an ordinary convolution (documentation)
  value: '1'
  source: https://docs.pytorch.org/docs/stable/generated/torch.nn.Conv2d.html
- setting: DeepLab, layers after the last stride is set to 1 in VGG-16 or ResNet-101 (DeepLab paper)
  value: '2'
  source: https://arxiv.org/abs/1606.00915
- setting: DeepLab, VGG-16's 7 × 7 fc6 layer as first adapted (DeepLab paper, Table 1)
  value: '4'
  source: https://arxiv.org/abs/1606.00915
- setting: DeepLab-LargeFOV, a 3 × 3 fc6 layer (DeepLab paper, Table 1)
  value: '12'
  source: https://arxiv.org/abs/1606.00915
- setting: Yu and Koltun's context module, layer 6 (their Table 1)
  value: '16'
  source: https://arxiv.org/abs/1511.07122
- setting: DeepLab ASPP-L, the largest of its rates 6, 12, 18, 24 (DeepLab paper)
  value: '24'
  source: https://arxiv.org/abs/1606.00915
mistakes:
- mistake: Thinking dilation adds weights or computation
  why: The DeepLab authors write that the number of filter parameters and of operations per position stay constant; Yu and Koltun's Figure 1 has the same parameters in every layer. In DeepLab's Table 1, a 3 × 3 fc6 at rate **12** matched the 7 × 7 at rate 4 (**67.64%** mean IOU after CRF) with **20.5M** parameters against **134.3M**, partly because they also kept only 1,024 of the 4,096 filters of fc6 and fc7.
  fix: Count a dilated layer as an ordinary k × k layer; attribute size changes to the filters.
- mistake: Using the same dilation in every layer
  why: 'Wang et al. call this gridding: with k = 3 and dilation 2 in each layer, a unit reads only **9** of the **25** cells of its patch, a checkerboard that loses at least 75% of the information. They use rates such as 1, 2, 3 in a row, with no common factor.'
  fix: Vary the dilation across consecutive layers so that the patches leave no holes.
- mistake: Calling the dilated convolution itself the à trous algorithm
  why: 'PyTorch''s documentation says dilation is "also known as the à trous algorithm". Yu and Koltun call this incorrect: the algorithme à trous decomposes a signal at several scales and uses dilated convolutions, but is not the operator itself. They also stress that no dilated filter is built.'
  fix: Say dilated (or atrous) convolution for the layer.
related:
- parameter/cnn-kernel-size
- parameter/cnn-padding
- parameter/cnn-conv-stride
visual:
  kind: conv_geometry
  image:
  - - 3
    - 1
    - 0
    - 2
    - 1
    - 0
    - 2
  - - 1
    - 4
    - 2
    - 0
    - 3
    - 1
    - 0
  - - 0
    - 2
    - 5
    - 1
    - 0
    - 2
    - 1
  - - 2
    - 0
    - 1
    - 3
    - 2
    - 0
    - 3
  - - 1
    - 3
    - 0
    - 2
    - 4
    - 1
    - 0
  - - 0
    - 1
    - 2
    - 0
    - 1
    - 3
    - 2
  - - 2
    - 0
    - 1
    - 3
    - 0
    - 2
    - 1
  filter:
  - - 1
    - 0
    - 1
  - - 0
    - 2
    - 0
  - - 1
    - 0
    - 1
  panels:
  - label: Dilation 1
    stride: 1
    padding: 0
    dilation: 1
    window:
      row: 1
      column: 1
  - label: Dilation 2
    stride: 1
    padding: 0
    dilation: 2
    window:
      row: 1
      column: 1
  caption: 'The same 3 × 3 filter on the same 7 × 7 grid at dilation 1 and dilation 2, no padding. At dilation 2 the nine taps (shaded) sit two cells apart and span the outlined 5 × 5 patch, so fewer positions fit: the output shrinks from 5 × 5 to 3 × 3 with the same nine weights. The grid and filter are an example, not from a trained model.'
tags:
- cnn
- convolution
- dilation
- atrous
- receptive-field
- vision
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/1511.07122
  title: Multi-Scale Context Aggregation by Dilated Convolutions (Yu & Koltun, arXiv:1511.07122)
  trust: 0.95
- url: https://arxiv.org/abs/1606.00915
  title: 'DeepLab: Semantic Image Segmentation with Deep Convolutional Nets, Atrous Convolution, and Fully Connected CRFs (Chen et al., arXiv:1606.00915)'
  trust: 0.9
- url: https://arxiv.org/abs/1702.08502
  title: Understanding Convolution for Semantic Segmentation (Wang et al., arXiv:1702.08502)
  trust: 0.9
- url: https://docs.pytorch.org/docs/stable/generated/torch.nn.Conv2d.html
  title: 'PyTorch documentation: torch.nn.Conv2d'
  trust: 0.9
- url: https://arxiv.org/abs/1603.07285
  title: A guide to convolution arithmetic for deep learning (Dumoulin & Visin, arXiv:1603.07285)
  trust: 0.9
---

## Definition

**dilation is the spacing between the filter's taps.** PyTorch's documentation says it "controls the
spacing between the kernel points"; DeepLab calls it the rate.

- **Dilation 1** is an ordinary convolution: the taps touch.
- **Dilation d** leaves d − 1 cells between neighbouring taps, so a k × k filter spans
  d × (k − 1) + 1 cells per side.
- **The weights are the same:** still k × k per input channel, only applied further apart.
- **The figure** shows the nine taps of one window at dilation 1 and 2.

**Analogy:** a comb whose teeth can be spread apart; it still has nine teeth, but they cover more
hair. The analogy shows the idea only; the exact rule is under Calculation.

## How It Works

**Spread the taps, slide as usual.**

1. **Spread.** Tap (m, n) of the filter reads the cell d × m rows and d × n columns from the
   window's corner (Yu and Koltun's l-dilated convolution).
2. **Multiply and add.** As in any convolution: weights times values, summed, plus the bias.
3. **Slide.** The window, now wider, fits fewer positions unless the padding grows.
4. **Stack.** Yu and Koltun double the dilation layer by layer, so the receptive field (the input
   cells one output depends on) grows exponentially while the parameters grow linearly.
5. **Keep resolution.** DeepLab sets a late layer's stride to 1 and dilates the layers after it by
   2, computing denser feature maps "without requiring learning any extra parameters".

## Calculation

**Dilation enters the span of the filter and, through it, the output size.**

In words: the span is the taps' distance times the gaps, plus one; the output size uses that span
in place of k.

$$
k_\text{e} = d\,(k-1) + 1 \qquad o = \left\lfloor \frac{i + 2p - d\,(k-1) - 1}{s} \right\rfloor + 1
$$

| Symbol | Meaning |
| --- | --- |
| $d$ | dilation |
| $k$, $k_\text{e}$ | kernel size, span it covers |
| $i$, $o$ | input and output side |
| $p$, $s$ | padding, stride |

The parameters, $k^2 C_\text{in} C_\text{out} + C_\text{out}$, have no $d$. For whole-network
counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**The figure's grid, then Yu and Koltun's context module and DeepLab's rate 12.**

1. **Dilation 1 on the 7 × 7 grid:** ⌊(7 − 1 × 2 − 1) ÷ 1⌋ + 1 = **5**.
2. **Dilation 2:** span 2 × 2 + 1 = 5; ⌊(7 − 2 × 2 − 1) ÷ 1⌋ + 1 = **3**.
3. **The outlined window at dilation 2** reads rows and columns 1, 3, 5: 1 × 3 + 1 × 1 + 2 × 5 +
   1 × 1 + 1 × 4 = **19** (zero weights left out).
4. **Context module, receptive field:** each 3 × 3 layer at dilation d adds 2d cells. Dilations
   1, 1, 2, 4, 8, 16 give 3, 5, 9, 17, 33 and **65**; the last 3 × 3 layer at dilation 1 makes
   **67**, as in their Table 1.
5. **Its parameters:** seven 3 × 3 layers on C channels, 7 × 9C², plus a 1 × 1 layer, C²: **64C²**,
   the "≈ 64C²" of the paper (biases left out).
6. **DeepLab-LargeFOV:** a 3 × 3 filter at rate 12 spans 12 × 2 + 1 = **25** cells per side, with 9
   weights per channel.

**What this means:** dilation buys a wider view at no cost in weights, but it thins out what each
layer reads, so rates are chosen layer by layer.

## What To Watch

- **Padding.** To keep the size at stride 1, pad by d × (k − 1) ÷ 2: 2 for a 3 × 3 at dilation 2.
- **Consecutive rates.** Rates with a common factor in a row leave holes (Wang et al.).
- **Stride together with dilation.** DeepLab removes a stride and dilates afterwards; torchvision's
  ResNet offers the same through replace_stride_with_dilation.
