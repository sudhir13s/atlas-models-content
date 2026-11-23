---
id: parameter/cnn-padding
type: parameter
slug: cnn-padding
schema_version: 3.1
title: Padding
name: padding
priority: 10
importance: optional
applies_to:
- cnn
range: 0 — 3
controls: How many rows and columns of zeros are added around the input before the filter slides over it.
effects:
  decrease:
  - 'A smaller output: with no padding a k × k filter at stride 1 loses k − 1 cells per side, as LeNet-5''s 5 × 5 C1 turns 32 into 28'
  - Edge cells take part in fewer windows than central ones
  - No change in parameters
  increase:
  - Padding (k − 1) ÷ 2 for odd k keeps the size at stride 1; VGG pads its 3 × 3 layers by 1
  - More positions to compute
  - No change in parameters
scale:
  min: 0
  max: 4
  typical_min: 0
  typical_max: 3
  spacing: linear
typical_values:
- setting: LeNet-5, layer C1, a 5 × 5 filter on a 32 × 32 input (LeCun et al., 1998)
  value: '0'
  source: http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf
- setting: VGG, every 3 × 3 convolution (VGG paper)
  value: '1'
  source: https://arxiv.org/abs/1409.1556
- setting: ResNet, 7 × 7 conv1 with stride 2 (torchvision resnet.py)
  value: '3'
  source: https://github.com/pytorch/vision/blob/main/torchvision/models/resnet.py
mistakes:
- mistake: Thinking padding adds parameters
  why: The added cells are fixed zeros (PyTorch's default padding_mode is 'zeros'), and Conv2d's weight shape, out_channels × in_channels × k × k, has no padding in it. VGG's 3 × 3 layers would have the same weights with or without their padding of 1.
  fix: Count the layer from k, C_in and C_out only; use padding for the output size.
- mistake: Assuming every network pads to keep its size
  why: 'LeNet-5 pads nothing: its C1 maps are **28 × 28** from a **32 × 32** input. The authors made the input larger than the largest digit (at most 20 × 20) so that strokes near the edge can still reach the centre of the top layers'' receptive fields.'
  fix: Read the padding of each layer; do not assume the output equals the input.
- mistake: Expecting same padding to keep the size at any stride
  why: PyTorch's documentation says padding='same' does not support strides other than 1. ResNet's conv1 pads by 3 with stride 2 and halves the size, 224 to **112**.
  fix: At stride above 1, compute the output with the formula under Calculation.
related:
- parameter/cnn-kernel-size
- parameter/cnn-conv-stride
- parameter/cnn-dilation
visual:
  kind: conv_geometry
  image:
  - - 3
    - 1
    - 0
    - 2
    - 1
  - - 1
    - 4
    - 2
    - 0
    - 3
  - - 0
    - 2
    - 5
    - 1
    - 0
  - - 2
    - 0
    - 1
    - 3
    - 2
  - - 1
    - 3
    - 0
    - 2
    - 4
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
  - label: Padding 0
    stride: 1
    padding: 0
    dilation: 1
    window:
      row: 1
      column: 1
  - label: Padding 1
    stride: 1
    padding: 1
    dilation: 1
    window:
      row: 1
      column: 1
  caption: The same 3 × 3 filter on the same 5 × 5 grid with no padding and with one ring of zeros. Without padding the first window sits inside the grid and the output is 3 × 3; with padding 1 the first window is centred on the corner cell, reads five zeros, and the output stays 5 × 5. The grid and filter are an example, not from a trained model.
tags:
- cnn
- convolution
- padding
- vision
updated: '2026-11-23'
sources:
- url: https://docs.pytorch.org/docs/stable/generated/torch.nn.Conv2d.html
  title: 'PyTorch documentation: torch.nn.Conv2d'
  trust: 0.9
- url: https://arxiv.org/abs/1603.07285
  title: A guide to convolution arithmetic for deep learning (Dumoulin & Visin, arXiv:1603.07285)
  trust: 0.9
- url: https://arxiv.org/abs/1409.1556
  title: Very Deep Convolutional Networks for Large-Scale Image Recognition (Simonyan & Zisserman, arXiv:1409.1556)
  trust: 0.95
- url: http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf
  title: Gradient-Based Learning Applied to Document Recognition (LeCun, Bottou, Bengio & Haffner, Proceedings of the IEEE 86(11), 1998)
  trust: 0.95
- url: https://github.com/pytorch/vision/blob/main/torchvision/models/resnet.py
  title: 'torchvision source: models/resnet.py'
  trust: 0.9
- url: https://arxiv.org/abs/1512.03385
  title: Deep Residual Learning for Image Recognition (He, Zhang, Ren & Sun, arXiv:1512.03385)
  trust: 0.95
---

## Definition

**padding is how many rows and columns of zeros are added on every side of the input** before the
filter slides over it. PyTorch's documentation calls it "padding added to all four sides of the
input".

- **Without padding** the window must fit inside the input, so the output is smaller.
- **With padding** the window can be centred on edge cells too.
- **It holds no weights:** the zeros are fixed, not learned.
- **The figure** shows the first window without and with one ring of zeros.

**Analogy:** a passe-partout around a photo, so a frame can be centred on the corner of the picture.
The analogy shows the idea only; the size rule is under Calculation.

## How It Works

**Add zeros, then slide as usual.**

1. **Pad.** p rows of zeros go above and below, p columns left and right: an i × i input becomes
   (i + 2p) × (i + 2p).
2. **Slide.** The filter visits every position that fits in the padded grid.
3. **Same padding.** Dumoulin and Visin call p = ⌊k ÷ 2⌋ for odd k "half (same) padding": at stride
   1 the output equals the input.
4. **Full padding.** p = k − 1 makes the output larger, i + k − 1 (Dumoulin and Visin).

## Calculation

**Padding enters the output size only.**

In words: padding widens the input by 2p before the window's span is subtracted.

$$
o = \left\lfloor \frac{i + 2p - d\,(k-1) - 1}{s} \right\rfloor + 1 \qquad p_\text{same} = \frac{k-1}{2} \ (k \text{ odd},\ s = 1)
$$

| Symbol | Meaning |
| --- | --- |
| $p$ | padding |
| $i$, $o$ | input and output side |
| $k$, $s$, $d$ | kernel size, stride, dilation |

The parameter count, $k^2 C_\text{in} C_\text{out} + C_\text{out}$, has no $p$. For whole-network
counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**The figure's grid, then three published layers.**

1. **No padding:** ⌊(5 + 0 − 2 − 1) ÷ 1⌋ + 1 = **3**, a 3 × 3 output.
2. **Padding 1:** ⌊(5 + 2 − 2 − 1) ÷ 1⌋ + 1 = **5**: the size is kept.
3. **The corner window with padding 1** reads the padded cells (0, 0, 0), (0, 3, 1), (0, 1, 4):
   1 × 0 + 1 × 0 + 2 × 3 + 1 × 0 + 1 × 4 = **10**, with the zero weights left out of the sum.
4. **How often edges are read** (counted on the grid): without padding the corner cell is in **1**
   window and the centre cell in **9**; with padding 1 the corner is in **4**.
5. **LeNet-5, C1** (5 × 5, no padding): 32 − 5 + 1 = **28**.
6. **VGG, a 3 × 3 layer** (padding 1): ⌊(224 + 2 − 2 − 1) ÷ 1⌋ + 1 = **224**.
7. **ResNet, conv1** (7 × 7, stride 2, padding 3): ⌊(224 + 6 − 6 − 1) ÷ 2⌋ + 1 = **112**.

**What this means:** padding decides whether a layer keeps its size and how much the edges count;
it never changes the number of weights.

## What To Watch

- **Kernel and padding together.** Same padding is (k − 1) ÷ 2: 1 for 3 × 3, 3 for 7 × 7.
- **Stride.** With stride above 1 the output shrinks whatever the padding.
- **padding_mode.** PyTorch also offers 'reflect', 'replicate' and 'circular' instead of zeros.
