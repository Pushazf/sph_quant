# SPHQuant: Efficient extreme low bit weight quantization for Vision-Language Models

**Kewei Zhang<sup>1</sup>, Zheng Chen<sup>1</sup>, Haotong Qin<sup>2</sup>, Yulun Zhang<sup>1,†</sup>**

<sup>1</sup> Shanghai Jiao Tong University &nbsp;&nbsp; <sup>2</sup> The Hong Kong Polytechnic University  
<sup>†</sup> Corresponding author.

## Abstract

Recent foundation models are moving toward native multimodal Vision-Language Models (VLMs), making VLMs a central form of next-generation foundation models. However, their large language backbones make edge deployment difficult due to high memory footprint and memory-bound autoregressive decoding. Weight-only post-training quantization is a practical solution, but pushing VLMs to extreme low bit-widths remains challenging: existing rotation-free methods suffer from outliers at 2–3 bits, while rotation-based methods improve accuracy at the cost of additional runtime overhead. We propose SPHQuant, a rotation-free spherical weight-only quantization framework for VLMs. Instead of quantizing weights directly in Cartesian coordinates, SPHQuant decomposes each 8D weight vector into coordinate signs, radius, and a positive unit direction. This representation isolates outlier magnitude into the radius while keeping directions bounded and statistically regular. Based on this insight, SPHQuant allocates extra precision to the radius to mitigate accuracy degradation induced by outliers. It further uses a compact positive-direction codebook and fine-tunes codebook entries through angular parameterization to preserve the unit-sphere constraint. We also design a hardware-friendly GEMV kernel that keeps the direction codebook small enough for shared-memory lookup and packs radial bits efficiently. Experiments show that SPHQuant matches the performance of state-of-the-art extreme low-bit quantization methods while improving decode throughput over QTIP by **30.3%** on RTX A6000. Code will be released.

## Overview

![Overview of SPHQuant: spherical weight quantization and packed extra radius bits](assets/overview.png)

## Main Results

| Model | Setting | Method | MMMU_val | TextVQA | ScienceQA | RealWorldQA | MMStar | Average |
|---|---|---|---:|---:|---:|---:|---:|---:|
| Qwen3-VL-8B-Instruct | BF16 | BF16 | 52.9 | 82.4 | 93.1 | 69.7 | 68.0 | 73.2 |
| Qwen3-VL-8B-Instruct | W2 | QTIP | 42.2 | **77.2** | 80.4 | 57.1 | **57.6** | 62.9 |
| Qwen3-VL-8B-Instruct | W2 | **SPHQuant (Ours)** | **42.2** | 76.8 | **81.0** | **57.4** | 57.5 | **63.0** |
| Qwen3-VL-8B-Instruct | W3 | RTN | 46.9 | 75.4 | 84.2 | 63.1 | 60.8 | 66.1 |
| Qwen3-VL-8B-Instruct | W3 | AWQ | 49.8 | 78.9 | 87.3 | 65.9 | 63.7 | 69.1 |
| Qwen3-VL-8B-Instruct | W3 | MBQ | 49.3 | 79.8 | 88.4 | 65.2 | 63.7 | 69.3 |
| Qwen3-VL-8B-Instruct | W3 | ParoQuant | 50.5 | 79.5 | 88.2 | 68.3 | 64.6 | 70.2 |
| Qwen3-VL-8B-Instruct | W3 | QTIP | **52.1** | 80.5 | **92.0** | **70.7** | 66.8 | 72.4 |
| Qwen3-VL-8B-Instruct | W3 | **SPHQuant (Ours)** | 51.7 | **81.0** | 91.8 | 69.9 | **67.6** | **72.4** |
| Qwen3.5-9B | BF16 | BF16 | 47.4 | 69.1 | 92.5 | 67.1 | 64.8 | 68.2 |
| Qwen3.5-9B | W2 | QTIP | **41.7** | 63.0 | **84.7** | **61.5** | 61.1 | **62.4** |
| Qwen3.5-9B | W2 | **SPHQuant (Ours)** | 41.4 | **63.7** | 83.0 | 61.3 | **61.7** | 62.2 |
| Qwen3.5-9B | W3 | RTN | 38.3 | 44.5 | 78.8 | 56.3 | 50.5 | 53.7 |
| Qwen3.5-9B | W3 | AWQ | 36.8 | 56.4 | 79.3 | 57.8 | 57.3 | 57.5 |
| Qwen3.5-9B | W3 | MBQ | 38.4 | 61.6 | 73.5 | 63.9 | 56.9 | 58.9 |
| Qwen3.5-9B | W3 | ParoQuant | 42.0 | 66.3 | 87.1 | 64.5 | 58.8 | 63.7 |
| Qwen3.5-9B | W3 | QTIP | 42.4 | **67.2** | 88.8 | 65.7 | 61.7 | 65.2 |
| Qwen3.5-9B | W3 | **SPHQuant (Ours)** | **43.7** | 66.3 | **90.0** | **66.3** | **63.0** | **65.9** |
| Gemma4-26B-A4B | BF16 | BF16 | 53.8 | 69.5 | 91.3 | 65.8 | 64.2 | 68.9 |
| Gemma4-26B-A4B | W3 | RTN | 46.7 | 62.8 | 83.2 | 59.1 | 59.1 | 62.2 |
| Gemma4-26B-A4B | W3 | AWQ | 48.3 | 66.1 | 84.7 | 58.7 | 62.3 | 64.0 |
| Gemma4-26B-A4B | W3 | MBQ | 49.0 | 64.2 | 87.2 | 60.5 | **63.5** | 64.9 |
| Gemma4-26B-A4B | W3 | **SPHQuant (Ours)** | **51.4** | **67.0** | **88.7** | **64.6** | 63.3 | **67.0** |

W2/W3 denote nominal quantization settings. SPHQuant W2 uses 2.25 encoded bits/weight; its Qwen3-VL-8B-Instruct W3 configuration uses 3.125. These payloads exclude group parameters and codebooks.

## To Do

- [ ] Add the paper and arXiv links.
- [ ] Release calibration, quantization, and CUDA inference code.
- [ ] Release evaluation scripts and reproduction instructions.
- [ ] Add the supplementary-material download.
