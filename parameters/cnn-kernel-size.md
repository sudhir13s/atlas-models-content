---
id: parameter/cnn-kernel-size
type: parameter
slug: cnn-kernel-size
schema_version: 2.9
title: "Kernel Size (k)"
name: kernel_size
priority: 3
importance: critical
applies_to: [cnn]
range: "1 — 11"
controls: "The height and width of each filter: how large a patch of the input one convolution reads at every position."
effects:
  decrease:
    - "Fewer weights per filter, with the square of the size: a 3 × 3 filter has 9/25 of a 5 × 5 filter's weights"
    - "Each layer reads a smaller patch; VGG reaches a 7 × 7 view by stacking three 3 × 3 layers"
    - "A larger output at the same padding and stride"
  increase:
    - "More weights with the square of the size: a 7 × 7 filter has 49/9, about 5.4 times, a 3 × 3 filter's weights"
    - "A wider patch per layer"
    - "In VGG's test, replacing each pair of 3 × 3 layers of net B with one 5 × 5 layer gave a top-1 error 7% higher"
scale:
  min: 1
  max: 12
  typical_min: 1
  typical_max: 11
  spacing: linear
typical_values:
  - setting: "VGG configuration C, its three 1 × 1 layers (VGG paper, Table 1)"
    value: "1"
    source: "https://arxiv.org/abs/1409.1556"
  - setting: "VGG, every convolution of configurations A, B, D and E (VGG paper)"
    value: "3"
    source: "https://arxiv.org/abs/1409.1556"
  - setting: "LeNet-5, layers C1, C3 and C5 (LeCun et al., 1998)"
    value: "5"
    source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf"
  - setting: "ResNet, first layer conv1 (ResNet paper, Table 1)"
    value: "7"
    source: "https://arxiv.org/abs/1512.03385"
  - setting: "AlexNet, first convolutional layer, 96 filters (AlexNet paper)"
    value: "11"
    source: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html"
mistakes:
  - mistake: "Assuming a larger view needs a larger kernel"
    why: >-
      The VGG authors note that two stacked 3 × 3 layers see a **5 × 5** patch and three see **7 × 7**.
      With C channels in and out, three 3 × 3 layers have **27C²** weights against **49C²** for one
      7 × 7 layer, which is **81%** more. In their test, a net with 5 × 5 layers in place of pairs of
      3 × 3 layers had a top-1 error **7%** higher.
    fix: "Stack small kernels when the aim is a wider view; compare the counts as in the figure."
  - mistake: "Counting a filter as k × k weights"
    why: >-
      PyTorch's Conv2d weight has the shape out_channels × in_channels × k × k: every filter spans all
      input channels. LeNet-5's C1 filters have **25** weights because the input has one channel;
      ResNet's 7 × 7 conv1 filters read 3 colour channels, **147** weights each.
    fix: "Multiply k × k by the input channels, then by the filters."
  - mistake: "Thinking a 1 × 1 kernel does nothing"
    why: >-
      It sees one position but mixes every channel there. VGG describes it as a linear transformation
      of the input channels; ResNet's bottleneck uses a 1 × 1 layer to narrow **256** channels to
      **64**, which costs 256 × 64 = **16,384** weights.
    fix: "Count a 1 × 1 layer as k = 1: in_channels × out_channels weights."
  - mistake: "Expecting the filter to be flipped, as in the textbook convolution"
    why: >-
      PyTorch's documentation describes Conv2d as the valid 2D **cross-correlation**: the filter is laid
      on the input as it is. A learned filter is simply learned the other way round, so no count or
      size changes.
    fix: "Read a filter's weights in the same order as the patch under it, as the figures here do."
related: [parameter/cnn-num-filters, parameter/cnn-padding, parameter/cnn-dilation, parameter/cnn-num-conv-layers]
visual:
  kind: conv_params
  groups:
    - label: "One 7 × 7"
      layers: [{ kernel: 7, in_channels: 64, out_channels: 64, count: 1, bias: false, part: "weights" }]
    - label: "Three 3 × 3"
      layers: [{ kernel: 3, in_channels: 64, out_channels: 64, count: 3, bias: false, part: "weights" }]
    - label: "One 5 × 5"
      layers: [{ kernel: 5, in_channels: 64, out_channels: 64, count: 1, bias: false, part: "weights" }]
    - label: "Two 3 × 3"
      layers: [{ kernel: 3, in_channels: 64, out_channels: 64, count: 2, bias: false, part: "weights" }]
  caption: "Weights of 7 × 7, 5 × 5 and 3 × 3 convolutions with 64 channels in and out, the width of VGG's first block, biases left out as the VGG paper counts them. Three 3 × 3 layers see the same 7 × 7 patch as one 7 × 7 layer with 27/49 of its weights; two see a 5 × 5 patch with 18/25."
tags: [cnn, convolution, kernel, receptive-field, parameters, vision]
updated: 2026-11-23
sources:
  - { url: "https://arxiv.org/abs/1409.1556", title: "Very Deep Convolutional Networks for Large-Scale Image Recognition (Simonyan & Zisserman, arXiv:1409.1556)", trust: 0.95 }
  - { url: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf", title: "Gradient-Based Learning Applied to Document Recognition (LeCun, Bottou, Bengio & Haffner, Proceedings of the IEEE 86(11), 1998)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1512.03385", title: "Deep Residual Learning for Image Recognition (He, Zhang, Ren & Sun, arXiv:1512.03385)", trust: 0.95 }
  - { url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html", title: "ImageNet Classification with Deep Convolutional Neural Networks (Krizhevsky, Sutskever & Hinton, NeurIPS 2012)", trust: 0.95 }
  - { url: "https://docs.pytorch.org/docs/stable/generated/torch.nn.Conv2d.html", title: "PyTorch documentation: torch.nn.Conv2d", trust: 0.9 }
  - { url: "https://github.com/pytorch/vision/blob/main/torchvision/models/resnet.py", title: "torchvision source: models/resnet.py", trust: 0.9 }
---

## Definition

**kernel_size is the height and width of each filter**, the patch of the input it reads at every
position. PyTorch's Conv2d takes it as kernel_size; papers write "3 × 3 conv".

- **A filter** (or kernel) is a small grid of learned weights, one grid per input channel.
- **At each position** it multiplies the patch under it by its weights and adds them up, giving one
  number of the output map.
- **Its cost grows with the square:** a k × k filter over C_in channels has k × k × C_in weights.
- **The figure** compares one large kernel with a stack of small ones at the same view.

**Analogy:** a magnifying glass moved over a page; a wider glass shows more at once but is heavier
to carry. The analogy shows the idea only; the counts are under Calculation.

## How It Works

**Lay the filter on a patch, multiply, add, move on.**

1. **Place.** The k × k window sits on the top-left patch of every input channel.
2. **Multiply and add.** Each weight multiplies the value under it; the products of all channels
   are summed and the filter's bias is added.
3. **Slide.** The window moves by the stride and repeats, filling one output map per filter.
4. **Stack.** Each 3 × 3 layer widens the view by 2 cells: VGG notes that two layers see 5 × 5 and
   three see 7 × 7, with a non-linear step between them.

## Calculation

**How the kernel size enters one layer's parameters and its output size.**

In words: each of the C_out filters has k × k weights for each of the C_in input channels, plus
one bias; the window's size also sets how many positions fit.

$$
P = k^2 \, C_\text{in} \, C_\text{out} + C_\text{out} \qquad o = \left\lfloor \frac{i + 2p - d\,(k-1) - 1}{s} \right\rfloor + 1
$$

| Symbol | Meaning |
| --- | --- |
| $k$ | kernel_size |
| $C_\text{in}$, $C_\text{out}$ | input channels, filters (output channels) |
| $i$, $o$ | input and output side |
| $p$, $s$, $d$ | padding, stride, dilation |

- **The bias term** is left out when the layer has none: torchvision's ResNet sets bias=False.
- For whole-network counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**First layers of three published networks, then VGG's argument with numbers.**

1. **LeNet-5, C1:** 6 filters of 5 × 5 on one channel: 6 × (25 + 1) = **156** parameters, the
   count the paper gives.
2. **ResNet, conv1:** 64 filters of 7 × 7 on 3 channels, no bias: 7 × 7 × 3 × 64 = **9,408**.
3. **AlexNet, first layer:** 96 filters of 11 × 11 × 3: 11 × 11 × 3 × 96 = **34,848** weights,
   plus 96 biases.
4. **One 3 × 3 layer, 64 channels in and out:** 3 × 3 × 64 × 64 = **36,864** weights.
5. **Three of them:** 3 × 36,864 = **110,592**, seeing a 7 × 7 patch.
6. **One 7 × 7 layer instead:** 7 × 7 × 64 × 64 = **200,704**, which is 49/27 ≈ 1.81 times as many:
   the **81%** more of the VGG paper.

**What this means:** the kernel size enters the count squared, so small kernels stacked deep give
the same view for fewer weights, and with more non-linear steps.

## What To Watch

- **Input channels.** The same k × k costs far more on 512 channels than on 3.
- **Padding.** A larger kernel needs more padding, (k − 1) ÷ 2 for odd k, to keep the size.
- **Bias.** Layers followed by batch normalization usually have none (torchvision ResNet).
