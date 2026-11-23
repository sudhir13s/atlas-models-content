---
id: model/lavasr
type: model
slug: lavasr
schema_version: 3.0
title: LavaSR
organization: organization/yatharths
release_date: '2026-02-11'
license: license/apache-2-0
model_category: audio
model_kind: audio-speech
tasks:
- task/audio-enhancement
summary: LavaSR v2 is a small audio enhancement model published by YatharthS on Hugging Face. It performs bandwidth extension, which rebuilds the high frequencies that low-quality or low-sample-rate audio has lost, and it is paired with a denoiser. The card says the model is 50 MB, accepts audio at any rate from 8 to 48 kHz, and outputs 48 kHz audio.
intended_use: 'The card lists three uses: restoring low-quality audio datasets, improving the quality of text-to-speech and speech-recognition models, and upscaling poor-quality voice calls.'
what_sets_it_apart: The card claims that the model can enhance nearly 5000 seconds of audio in one second, and that its quality exceeds that of diffusion models of 6 GB. It gives an inference speed of 20 to 80 times real time on a CPU and 800 to 5000 times real time depending on the GPU.
pipeline:
  source: https://github.com/ysharma3501/LavaSR/blob/main/LavaSR/enhancer/enhancer.py
  summary: Low-quality speech is optionally denoised, resampled to 48 kHz and turned into a mel spectrogram. A Vocos network of convolution blocks predicts a full-band spectrum in one pass, an inverse transform turns it into sound, and a final merge keeps the original low frequencies and takes the high ones from the prediction.
  stages:
  - id: audio-in
    role: input
    title: Low-quality audio
    details:
    - any rate from 8 to 48 kHz
  - id: denoiser
    role: step
    title: UL-UNAS denoiser
    details:
    - optional
    note: A separate denoising model, based on UL-UNAS, that the package runs first when denoising is switched on.
  - id: resample
    role: step
    title: Resample to 48 kHz
    note: Plain resampling to the output rate; the missing high frequencies are still missing at this point.
  - id: mel
    role: step
    title: Mel spectrogram
    details:
    - 80 bins · hop 512
  - id: embed
    role: embedding
    title: Input convolution
    details:
    - kernel 7 · width 512 · LayerNorm
  - id: convnext
    role: feedForward
    title: ConvNeXt block
    details:
    - width 512 · inner width 1536
    note: A depthwise convolution followed by a two-layer feed-forward part with a skip connection. The time resolution stays the same through every block.
    frame: Vocos backbone
    repeat: 8
  - id: final-norm
    role: normalisation
    title: Final LayerNorm
  - id: istft-head
    role: outputHead
    title: iSTFT head
    details:
    - magnitude + phase · n_fft 2048
    note: A linear layer predicts the magnitude and phase of a spectrum for each frame, and an inverse short-time Fourier transform (STFT) turns it into samples.
  - id: band-merge
    role: step
    title: Band merge at cutoff
    details:
    - low band from input, high from model
    note: A merge inspired by Linkwitz-Riley crossovers keeps the input below the cutoff (by default about half the input sample rate) and the model's output above it, with a smooth fade between the two.
  - id: audio-out
    role: outputHead
    title: Enhanced audio
    details:
    - 48 kHz
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: audio-to-audio
hub_signals:
  downloads_30d: 1276
  likes: 91
  last_modified: '2026-02-27'
  observed_at: '2026-11-21'
  source: https://huggingface.co/YatharthS/LavaSR
aliases:
- YatharthS/LavaSR
lineage:
  parents: []
  children: []
quotes:
  summary: LavaSR(v2) is a novel 50MB BWE(bandwidth extension) model along with the UL-UNAS denoiser.
  intended_use: Restore low quality audio datasets
  what_sets_it_apart: It can enhance nearly 5000 seconds of audio in just 1 second while exceeding the quality of 6gb large diffusion models.
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/YatharthS/LavaSR
  title: YatharthS/LavaSR — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/YatharthS/LavaSR/blob/main/config.json
  title: YatharthS/LavaSR — config.json
  trust: 0.9
- url: https://github.com/ysharma3501/LavaSR/blob/main/LavaSR/enhancer/enhancer.py
  title: 'ysharma3501/LavaSR — LavaSR/enhancer/enhancer.py: bandwidth-extension model (GitHub)'
  trust: 0.9
- url: https://github.com/ysharma3501/LavaSR/blob/main/LavaSR/model.py
  title: 'ysharma3501/LavaSR — LavaSR/model.py: denoise, resample and enhance steps (GitHub)'
  trust: 0.9
- url: https://huggingface.co/YatharthS/LavaSR/blob/main/enhancer_v2/config.yaml
  title: YatharthS/LavaSR — enhancer_v2/config.yaml
  trust: 0.9
- url: https://github.com/gemelo-ai/vocos/blob/main/vocos/models.py
  title: 'gemelo-ai/vocos — vocos/models.py: Vocos backbone (GitHub)'
  trust: 0.9
---


