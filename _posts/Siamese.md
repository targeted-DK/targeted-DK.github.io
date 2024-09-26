---
layout: post
title: "Siamese Representation Learning"
date: 2024-09-25
categories: papers
---

# Exploring Simple Siamese Representation Learning

## Overview

Siamese 네트워크는 비지도 visual 표현 학습에서 자주 쓰이는 네트워크이다. 한 이미지로부터 두 개의 증강 이미지의 유사도를 비교하여 붕괴되는 현상을 막는데 초점을 두고있다. 
**Simple Siamese Network**를 사용하여 네거티브 쌍, 큰 배치 사이즈, 모멘텀 인코더 없이 학습할 수 있다는 것을 보여줌. Stop-gradient에 대한 가설을 보여주공 **SimSiam** 방법이 ImageNet의 downstream task에 비슷한 결과를 나타냄.


## Introduction
기본적으로 Siamese Network는 두 개 이상의 input에 대하여 신경망의 weight을 공유하는 네트워크이다. 가장 최근의 방법은 다른 조건 하에서 한 이미지의 증강된 두 이미지의 유사도를 측정하는 방식이었다.
문제는 이 네트워크가 상수로 붕괴되는 현상이 발견되곤 함. 이를 방지하기 위해 SimCLR을 이용한 Contrastive Learning을 통해 이런 현상을 방지할 수 있었음.
Clustering 또한 결과물이 상수로 나타내는 현상을 방지할 수 있으며, SwAV 모델이 Siamese network에 online clustering을 추가하는 방법도 있음. BYOL같은 경우는 유사 쌍만 사용하지만 모멘텀 인코더가 사용되는 경우 모델이 붕괴하지 않음.
즉 위 모델들은 출력물이 특정 상수로 붕괴하는 현상을 방지하기 위해 여러 방법을 쓰는데 간단한 simple Siamese Network(SimSiam)는 위해 제시된 방법들을 사용하지 않아도 모델이 붕괴하는 현상을
막을 수 있음. 이 모델은 한 이미지의 두 개 시점의 유사도를 극대화하며, 이 방법은 negative pair나 momemtum encoder를 사용하지 않음.
개념적으로 간단하기 때문에 쉽게 설명하자면 "BYOL without the momentum encoder" 또는 "SimCLR without negative pairs", "SwAV without online clustering"으로도 표현할 수 있음.
즉 각 모델(SimCLR, BYOL, SAV)에서 중요한 부분을 제거한 방법이라고 할 수 있음.

모델이 붕괴하는 경우도 있지만 stop-gradient operation을 통해 방지할 수 잇음. 같은 객체에 대한 두 개의 다른 관찰은 결국 같은 부분을 학습하여 inductive bias가질 수 있으며, Siamese network 또한
더 복잡한 변환을 거친 이미지에 대해서도 이러한 불변성을 가질 수 있음.
![](/images/Siamese/1.png)

| **Model**   | **Approach** | **Main Objective** | **Key Innovations** | **Loss Function** | **Negative Sampling** | **Memory Bank** | **Architectural Features** | **Applications** |
|-------------|--------------|--------------------|---------------------|-------------------|-----------------------|-----------------|----------------------------|------------------|
| **CLIP** (Contrastive Language-Image Pretraining) | **Contrastive learning between image and text pairs** | Align image and text representations in a shared embedding space to enable zero-shot classification. | - Uses large-scale datasets of image-text pairs.<br>- Employs a dual-encoder architecture (one for images, one for text). | Contrastive loss (InfoNCE) between text and image embeddings. | Negative examples are provided by other samples in the batch (softmax across all text-image pairs). | No | Dual encoder: image encoder (e.g., ResNet, Vision Transformer) and text encoder (e.g., Transformer). | Multimodal tasks: image-text matching, zero-shot image classification, text-guided image retrieval. |
| **SimCLR** (Simple Contrastive Learning) | **Contrastive learning** with strong augmentations | Learn visual representations by contrasting different views (augmentations) of the same image. | - Requires no memory bank.<br>- Uses strong data augmentations.<br>- Introduces a projection head to improve representations. | Contrastive loss (InfoNCE) on augmented views of the same image. | Large batch size for creating enough negative examples. | No | Single encoder with projection head (MLP). | Unsupervised pretraining for image classification and other visual tasks. |
| **MoCo** (Momentum Contrast) | **Contrastive learning with memory bank** | Maintain a large and consistent set of negative samples using a memory bank and momentum encoder. | - Introduces a momentum encoder to create stable, slowly-updating negative keys.<br>- Uses a memory bank to store negative examples from previous batches. | Contrastive loss (InfoNCE) | Yes, stored in a dynamic memory bank. | Yes | Two encoders: online encoder and momentum encoder. Memory bank stores negative samples. | Visual representation learning for downstream tasks (image classification, detection). |
| **SwAV** (Swapping Assignments Between Views) | **Clustering-based contrastive learning** | Learn visual representations by assigning different views of an image to the same cluster prototype. | - Uses online clustering to replace negative sampling.<br>- Swaps cluster assignments between augmented views.<br>- Works well with small batches. | Swapped clustering loss: learns consistency between cluster assignments of different views. | No, uses clustering prototypes instead. | No | Encoder with projection head. Cluster assignment mechanism. | Unsupervised learning for image classification, object detection, segmentation. |
| **BYOL** (Bootstrap Your Own Latent) | **Self-supervised learning without negative samples** | Learn robust visual representations without using negative samples or a memory bank. | - Eliminates the need for negative pairs.<br>- Introduces a momentum encoder to provide stable targets.<br>- Uses a predictor on the online network to predict the output of the target network. | Loss that matches the output of the online encoder with the momentum encoder's output (L2 distance). | No | No | Two networks: online network and momentum network. The online network has an additional prediction head. | Unsupervised pretraining for image classification and other downstream visual tasks. |

## Method
이 모델 구조는 이미지 x에서 두 개의 증강된 시점 $x_{1}$, $x_{2}$를 만들고, 두 시점을 ResNet, MLP head로 이루어진 인코더 $f$에
넣는다. 이 인코더는 두 시점의 가중치를 서로 공유한다. $h$ 경우 예측 MLP head를 뜻하며, 이는 하나의 시점을 다른 시점으로 변환하는 역할을 수행함. 여기서 출력되는 두 출력 벡터 $p_{1}$와 $z_2$ 의 음의 코사인 유사도를 최소화한다. 
$D(p_1, z_2) = - \frac{p_1 \cdot z_2}{\||p_1\||_2 \||z_2\||_2}$

이는 $l_{2}$ 정규화의 평균제곱오차이며 scale이 2인 공식과 같다.
손실 함수는 다음과 같음 : $L = 2D(p_1, z_2) + 2D(p_2, z_1)$.

이 구조에서 가장 중요한 stop-gradient operation은 다음과 같음.
$D(p_1, \text{stopgrad}(z_2))$

손실 함수와 stop-gradient operation 함수를 합치면 다음과 같음.
$L = 2D(p_1, \text{stopgrad}(z_2)) + 2D(p_2, \text{stopgrad}(z_1))$.
여기서 주목해야 할 점은 첫 번째 항에서 $x_2$의 인코더는 $z_2$로부터 아무 가중치를 받지 않지만, 두 번째 항에서 $p_2$로부터는 받음.($x_1$도 동일함)


