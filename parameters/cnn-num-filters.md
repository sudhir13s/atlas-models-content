---
id: parameter/cnn-num-filters
type: parameter
slug: cnn-num-filters
schema_version: 2.9
title: "Number of Filters (out_channels)"
name: out_channels
priority: 1
importance: critical
applies_to: [cnn]
range: "6 — 2,048"
controls: "How many filters a convolution layer learns; each filter produces one output map, so this is also the number of output channels."
effects:
  decrease:
    - "Fewer output maps, so fewer features found at each position"
    - "Parameters fall in a straight line in this layer, and the next layer, which reads these maps, shrinks too"
    - "Less computation in both layers"
  increase:
    - "More maps; this layer's parameters grow in a straight line, and so do the next layer's"
    - "VGG doubles the filters after each max-pooling layer, from 64 up to 512"
    - "ResNet doubles them whenever the map size halves, to keep the time per layer the same"
scale:
  min: 4
  max: 4096
  typical_min: 6
  typical_max: 2048
  spacing: log
typical_values:
  - setting: "LeNet-5, layer C1 (LeCun et al., 1998)"
    value: "6"
    source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf"
  - setting: "LeNet-5, layer C3 (LeCun et al., 1998)"
    value: "16"
    source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf"
  - setting: "VGG, first layer of every configuration (VGG paper)"
    value: "64"
    source: "https://arxiv.org/abs/1409.1556"
  - setting: "AlexNet, first convolutional layer (AlexNet paper)"
    value: "96"
    source: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html"
  - setting: "VGG, last two blocks (VGG paper)"
    value: "512"
    source: "https://arxiv.org/abs/1409.1556"
  - setting: "ResNet-50, last 1 × 1 layer of each conv5_x block (ResNet paper, Table 1)"
    value: "2048"
    source: "https://arxiv.org/abs/1512.03385"
mistakes:
  - mistake: "Counting a change of filters in one layer only"
    why: >-
      This layer's filters are the next layer's input channels. In VGG-16, conv3_1 (128 to 256) has
      **295,168** parameters and conv3_2 (256 to 256) **590,080**. Halving conv3_1 to 128 filters cuts
      it to **147,584** and conv3_2 to **295,168**: the pair falls from 885,248 to 442,752.
    fix: "Recount the layer after it whenever the filters change."
  - mistake: "Assuming each filter reads every input channel"
    why: >-
      In the plain case it does (PyTorch's weight is out × in × k × k). But AlexNet's second layer has
      filters of **5 × 5 × 48**, not × 96, because each reads only the maps on its own GPU; LeNet-5's C3
      maps each read a subset of S2's maps, giving **1,516** parameters instead of a full connection.
    fix: "Check whether the layer connects to all input channels (groups in PyTorch) before counting."
  - mistake: "Adding a bias per filter in every network"
    why: >-
      VGG's layers have one (conv1_1 has 1,728 weights and **64** biases), but torchvision's ResNet
      creates its convolutions with bias=False because batch normalization follows; Ioffe and Szegedy
      write that the bias is then cancelled by the mean subtraction.
    fix: "Add C_out to the count only when the layer has a bias."
related: [parameter/cnn-kernel-size, parameter/cnn-num-conv-layers, parameter/cnn-batch-norm]
visual:
  kind: conv_params
  groups:
    - { label: "conv1_1, 64", layers: [{ kernel: 3, in_channels: 3, out_channels: 64, count: 1, bias: true, part: "conv" }] }
    - { label: "conv1_2, 64", layers: [{ kernel: 3, in_channels: 64, out_channels: 64, count: 1, bias: true, part: "conv" }] }
    - { label: "conv2_1, 128", layers: [{ kernel: 3, in_channels: 64, out_channels: 128, count: 1, bias: true, part: "conv" }] }
    - { label: "conv2_2, 128", layers: [{ kernel: 3, in_channels: 128, out_channels: 128, count: 1, bias: true, part: "conv" }] }
    - { label: "conv3_1, 256", layers: [{ kernel: 3, in_channels: 128, out_channels: 256, count: 1, bias: true, part: "conv" }] }
    - { label: "conv3_2, 256", layers: [{ kernel: 3, in_channels: 256, out_channels: 256, count: 1, bias: true, part: "conv" }] }
    - { label: "conv3_3, 256", layers: [{ kernel: 3, in_channels: 256, out_channels: 256, count: 1, bias: true, part: "conv" }] }
    - { label: "conv4_1, 512", layers: [{ kernel: 3, in_channels: 256, out_channels: 512, count: 1, bias: true, part: "conv" }] }
    - { label: "conv4_2, 512", layers: [{ kernel: 3, in_channels: 512, out_channels: 512, count: 1, bias: true, part: "conv" }] }
    - { label: "conv4_3, 512", layers: [{ kernel: 3, in_channels: 512, out_channels: 512, count: 1, bias: true, part: "conv" }] }
    - { label: "conv5_1, 512", layers: [{ kernel: 3, in_channels: 512, out_channels: 512, count: 1, bias: true, part: "conv" }] }
    - { label: "conv5_2, 512", layers: [{ kernel: 3, in_channels: 512, out_channels: 512, count: 1, bias: true, part: "conv" }] }
    - { label: "conv5_3, 512", layers: [{ kernel: 3, in_channels: 512, out_channels: 512, count: 1, bias: true, part: "conv" }] }
  caption: "The parameters of each of VGG-16's 13 convolutional layers (configuration D of the VGG paper's Table 1; names and filters on the left), 3 × 3 filters with a bias each, as torchvision builds them. The count grows with the layer's own filters and with the filters of the layer before; the 13 layers sum to 14,714,688."
tags: [cnn, convolution, channels, filters, width, parameters, vision]
updated: 2026-11-23
sources:
  - { url: "https://arxiv.org/abs/1409.1556", title: "Very Deep Convolutional Networks for Large-Scale Image Recognition (Simonyan & Zisserman, arXiv:1409.1556)", trust: 0.95 }
  - { url: "https://github.com/pytorch/vision/blob/main/torchvision/models/vgg.py", title: "torchvision source: models/vgg.py", trust: 0.9 }
  - { url: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf", title: "Gradient-Based Learning Applied to Document Recognition (LeCun, Bottou, Bengio & Haffner, Proceedings of the IEEE 86(11), 1998)", trust: 0.95 }
  - { url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html", title: "ImageNet Classification with Deep Convolutional Neural Networks (Krizhevsky, Sutskever & Hinton, NeurIPS 2012)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1512.03385", title: "Deep Residual Learning for Image Recognition (He, Zhang, Ren & Sun, arXiv:1512.03385)", trust: 0.95 }
  - { url: "https://github.com/pytorch/vision/blob/main/torchvision/models/resnet.py", title: "torchvision source: models/resnet.py", trust: 0.9 }
  - { url: "https://docs.pytorch.org/docs/stable/generated/torch.nn.Conv2d.html", title: "PyTorch documentation: torch.nn.Conv2d", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1502.03167", title: "Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift (Ioffe & Szegedy, arXiv:1502.03167)", trust: 0.95 }
---

## Definition

**out_channels is how many filters a convolution layer learns.** PyTorch's documentation describes
it as the "number of channels produced by the convolution".

- **Each filter** is a k × k grid of weights for every input channel, plus usually one bias.
- **Each filter gives one output map**, a grid saying where its pattern appears.
- **The next layer reads these maps** as its input channels, so the choice affects two layers.
- **The figure** counts VGG-16's 13 convolutional layers one by one.

**Analogy:** a team of inspectors, each looking for one kind of flaw and marking it on their own
copy of the plan. The analogy shows the idea only; the counts are under Calculation.

## How It Works

**Many filters on the same input, one map each.**

1. **Same window, many filters.** At each position all C_out filters read the same patch of all
   C_in input channels.
2. **One number per filter.** Each gives its weighted sum plus bias: C_out numbers per position.
3. **Stack the maps.** The output has C_out channels, and the next layer's filters span all of them.
4. **Grow with depth.** VGG starts at 64 filters and doubles after each max-pooling layer until 512;
   ResNet doubles when the map size halves "so as to preserve the time complexity per layer".

## Calculation

**One layer's parameters, and how the next layer depends on them.**

In words: each filter has k × k weights per input channel plus a bias; the next layer's weights
use this layer's filters as their input channels.

$$
P_\ell = k^2 \, C_{\ell-1} \, C_\ell + C_\ell \qquad P_{\ell+1} = k^2 \, C_\ell \, C_{\ell+1} + C_{\ell+1}
$$

| Symbol | Meaning |
| --- | --- |
| $C_\ell$ | out_channels of layer ℓ |
| $C_{\ell-1}$ | its input channels (the filters of the layer before) |
| $k$ | kernel size |

$C_\ell$ appears in both counts. For whole-network counts, see the
[parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**LeNet-5's first layer, then VGG-16's** (layer shapes from each paper; VGG-16's biases as
torchvision builds it).

1. **LeNet-5, C1:** 6 filters × (5 × 5 × 1 + 1) = **156**, the count the paper gives.
2. **VGG-16, conv1_1:** 3 × 3 × 3 × 64 + 64 = 1,728 + 64 = **1,792**.
3. **conv1_2:** 3 × 3 × 64 × 64 + 64 = **36,928**: same filters, but 64 input channels instead of 3.
4. **conv3_1:** 3 × 3 × 128 × 256 + 256 = **295,168**.
5. **conv5_3:** 3 × 3 × 512 × 512 + 512 = **2,359,808**.
6. **All 13 layers:** **14,714,688**, the sum drawn in the figure.
7. **Halving conv3_1 to 128 filters:** 147,584 for it and 295,168 for conv3_2, which now reads 128
   channels: the pair falls from **885,248** to **442,752**.

**What this means:** the filter count multiplies into two layers at once, which is why the widest
layers, deep in the network, hold most of the convolutional weights.

## What To Watch

- **Bias.** Present in VGG and LeNet-5; absent in torchvision's ResNet, where batch normalization
  follows.
- **Groups.** A grouped or partly connected layer (AlexNet's second layer, LeNet-5's C3) reads only
  some input channels.
- **The head.** In VGG the fully connected layers hold far more than all the filters: see the
  number of convolutional layers page.
