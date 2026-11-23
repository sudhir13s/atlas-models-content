---
id: parameter/cnn-batch-norm
type: parameter
slug: cnn-batch-norm
schema_version: 2.9
title: "Batch Normalization (γ, β)"
name: batch_norm
priority: 6
importance: important
applies_to: [cnn]
range: "2 learned numbers per channel"
controls: "Whether each convolution's output is normalised per channel with the mean and variance of the mini-batch and then rescaled by a learned γ and shifted by a learned β."
options:
  - value: "Without"
    effect: "No γ, β or running statistics; each convolution keeps its own bias (VGG's configurations)."
  - value: "After each convolution"
    effect: "2 learned numbers per channel (γ, β) and 2 tracked statistics per channel that are not learned; the convolution's bias can be dropped (Ioffe and Szegedy; torchvision's ResNet)."
typical_values:
  - setting: "ResNet: after each convolution and before the activation (ResNet paper)"
    value: "on"
    source: "https://arxiv.org/abs/1512.03385"
  - setting: "VGG configurations A to E: no batch normalization in Table 1 (VGG paper)"
    value: "off"
    source: "https://arxiv.org/abs/1409.1556"
  - setting: "PyTorch BatchNorm2d, eps added to the variance (documentation default)"
    value: "0.00001"
    source: "https://docs.pytorch.org/docs/stable/generated/torch.nn.BatchNorm2d.html"
  - setting: "PyTorch BatchNorm2d, momentum of the running statistics (documentation default)"
    value: "0.1"
    source: "https://docs.pytorch.org/docs/stable/generated/torch.nn.BatchNorm2d.html"
  - setting: "ResNet-50, γ and β of all its batch-norm layers, counted from torchvision's code"
    value: "53,120"
    source: "https://github.com/pytorch/vision/blob/main/torchvision/models/resnet.py"
mistakes:
  - mistake: "Counting the running mean and variance as parameters"
    why: >-
      PyTorch's documentation calls running_mean and running_var buffers: they are tracked during
      training, not learned by the optimiser. Only γ and β (affine=True) are parameters, so ResNet-50's
      batch-norm layers add **53,120** of its **25,557,032** parameters, 0.21%.
    fix: "Count 2 × channels per layer as parameters, and the running statistics separately."
  - mistake: "Normalising each position of a feature map on its own"
    why: >-
      For convolutional layers Ioffe and Szegedy normalise all values of a feature map together, over
      the mini-batch and every location: for m images of p × q maps, m × p × q values. They learn one
      pair γ, β per feature map, not per activation.
    fix: "Compute one mean and one variance per channel, as in the figure."
  - mistake: "Expecting the same output in training and in use"
    why: >-
      In training the layer uses the batch's own mean and variance (PyTorch divides by n). In use the
      paper normalises with population statistics and the unbiased variance, n ÷ (n − 1) times the
      average batch variance; PyTorch uses its running estimates in evaluation mode.
    fix: "Switch the model to evaluation mode before using it, so the stored statistics are used."
  - mistake: "Keeping a bias in the convolution before batch normalization"
    why: >-
      Ioffe and Szegedy write that the bias can be ignored because the mean subtraction cancels it; its
      role is taken by β. torchvision's ResNet builds its convolutions with bias=False.
    fix: "Drop the convolution's bias when batch normalization follows it."
related: [parameter/cnn-num-filters, parameter/cnn-num-conv-layers, parameter/normalization]
visual:
  kind: batch_norm
  values:
    - [2, 4, 4, 4]
    - [5, 5, 7, 9]
  gamma: 2
  beta: 1
  epsilon: 0.00001
  worked: { row: 1, column: 1 }
  caption: "One channel of a mini-batch of two images whose maps are 2 × 2: each row holds one image's four values. The mean and variance are taken over all eight values together, as in training; then each value is normalised and multiplied by γ = 2 and shifted by β = 1, with PyTorch's default ε. The values, γ and β are an example, not from a trained model."
tags: [cnn, normalization, batchnorm, training, parameters, vision]
updated: 2026-11-23
sources:
  - { url: "https://arxiv.org/abs/1502.03167", title: "Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift (Ioffe & Szegedy, arXiv:1502.03167)", trust: 0.95 }
  - { url: "https://docs.pytorch.org/docs/stable/generated/torch.nn.BatchNorm2d.html", title: "PyTorch documentation: torch.nn.BatchNorm2d", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1512.03385", title: "Deep Residual Learning for Image Recognition (He, Zhang, Ren & Sun, arXiv:1512.03385)", trust: 0.95 }
  - { url: "https://github.com/pytorch/vision/blob/main/torchvision/models/resnet.py", title: "torchvision source: models/resnet.py", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1409.1556", title: "Very Deep Convolutional Networks for Large-Scale Image Recognition (Simonyan & Zisserman, arXiv:1409.1556)", trust: 0.95 }
---

## Definition

**Batch normalization (BN) rescales each channel of a layer's output** using the mean and variance
of that channel over the current mini-batch, then applies a learned scale γ and shift β.

- **A mini-batch** is the group of images processed together in one training step.
- **Normalise:** subtract the mean and divide by the standard deviation, so the values have mean 0
  and variance 1.
- **Scale and shift:** y = γ × x̂ + β. Ioffe and Szegedy note that γ and β let the layer undo the
  normalisation if that is best.
- **The figure** works through one channel of two small maps.

**Analogy:** grading on a curve, then letting each subject choose its own average and spread. The
analogy shows the idea only; the exact steps are under Calculation.

## How It Works

**Per channel: statistics, normalise, scale and shift.**

1. **Gather.** For one channel, take every value of every image in the batch, m × p × q values.
2. **Statistics.** Their mean μ and variance σ², the variance divided by the number of values.
3. **Normalise.** x̂ = (x − μ) ÷ √(σ² + ε); ε, 0.00001 in PyTorch, avoids dividing by zero.
4. **Scale and shift.** y = γ × x̂ + β, with one learned γ and β per channel.
5. **Track.** PyTorch keeps running estimates of μ and σ² (momentum 0.1) for use after training.
6. **Where.** ResNet places BN "right after each convolution and before activation".

## Calculation

**The transform and its parameter count.**

In words: centre and scale each value with its channel's batch statistics, then apply the
channel's γ and β; only γ and β are learned.

$$
\hat{x} = \frac{x - \mu_B}{\sqrt{\sigma_B^2 + \epsilon}} \qquad y = \gamma \hat{x} + \beta \qquad P_\text{BN} = 2C
$$

| Symbol | Meaning |
| --- | --- |
| $\mu_B$, $\sigma_B^2$ | mean and variance of the channel over the mini-batch |
| $\epsilon$ | small constant, 0.00001 in PyTorch |
| $\gamma$, $\beta$ | learned scale and shift, one each per channel |
| $C$ | channels |

The running mean and variance add 2C stored values that are not parameters. For whole-network
counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**The figure's channel, then ResNet-50** (its layers from torchvision's code).

1. **Mean:** (2 + 4 + 4 + 4 + 5 + 5 + 7 + 9) ÷ 8 = 40 ÷ 8 = **5**.
2. **Variance:** squared distances 9, 1, 1, 1, 0, 0, 4, 16 sum to 32; 32 ÷ 8 = **4**.
3. **Normalise the first value:** (2 − 5) ÷ √(4 + 0.00001) ≈ −3 ÷ 2 = **−1.5**.
4. **Scale and shift:** 2 × (−1.5) + 1 = **−2**. The largest value, 9, becomes 2 × 2 + 1 = **5**.
5. **One ResNet-50 layer of 256 channels:** 2 × 256 = **512** parameters.
6. **All of ResNet-50:** its 53 BN layers hold 2 × 26,560 = **53,120** parameters, 0.21% of
   25,557,032.
7. **What it buys:** Ioffe and Szegedy report that their BN-x5 network reached Inception's 72.2%
   accuracy in **14 times** fewer training steps.

**What this means:** batch normalization costs almost no parameters; its effect is on training,
and it makes the convolution's bias unnecessary.

## What To Watch

- **Training against evaluation.** Batch statistics in training, stored statistics in use.
- **Small batches.** The statistics come from the batch, so they depend on how many values it holds.
- **Bias.** A convolution followed by BN needs none.
