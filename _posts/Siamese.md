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

Optimizer는 SGD를 사용하며, Projection ML에서는 BN이 추가된 두 개의FC layer를 가지고 있으며 마지막 output layer에는 ReLU는없고 BN만 있는 레이어임.
Prediction MLP에서는 BN과 ReLU 둘 다 포함하지 않으며 두 개의 레이어가 있음. 입력과 출력 백터의 크기는 2048이며 중간의 hidden layer의 크기가 512이어서 h가 병목구조를 가지게 됨.

ResNet-50을 기본 뼈대로 사용하였으며 100-epoch pre-training을 거침.

ImageNet 훈련 데이터 셋의 1000개 class로 비지도 학습 사전 학습을 진행하였음. 평가에는 학습된 prepresentation을 고정시켜 supervised linear classifier을 사용하고 이를 validation set에 사용하였음.

## Empirical Study
모델이 stop-gradient를 사용하지 않으면 빠르게 붕괴하고, 출력된 표현이 모든 입력에 대해 거의 동일해짐(빨간 곡선). 이 때문에 표준편차가 0에 가까워지는데, 이는 출력값이 모든 입력에 대해 거의 동일하다는 것을 의미하며, 붕괴가 발생했음을 나타냄.

모델이 stop-gradient를 사용할 때는, 표현이 다양성을 유지하며, 표준편차가 $\sqrt(1/d)$ 근처에 머무름(파란 곡선). 이는 출력값이 잘 분포되어 있음을 시사하며, 모델이 붕괴하지 않고 있음을 나타냄. 출력값에 변동성이 존재하며, 이는 잘 학습된 표현에서 보여지는 모습임.
![](/images/Siamese/2.png)

이 실험에서는 predictor MLP가 모델의 성능에 미치는 영향을 확인했는데, 특히 $h$(predictor MLP)가 중요함.
$h$가 $I$일 경우 모델은 붕괴하며, random init 또한 의미가 없음. learning rate가 cosine decay거나 고정되어있을때 정확도가 올라가는데, 이는 lr를 수렴시키지 않음으로써 충분한 표현력을 학습시키는데 도움을 주는것으로 판단함. 

배치 정규화의 경우 모델 붕괴를 막는데 중요한 역할을 하지 않음. BN보다 더 중요한건 stop-gradient 사용 유무라고 판단함. 

코사인 유사도 말고 교차-엔트로피 유사도를 사용해 실험을 진행함. $D(p_1,z_2)=−softmax(z_2)·log softmax(p_1)$. 코사인 유사도의 정확도가 더 높지만 교차-엔트로피 유사도 또한 모델 붕괴를 방지함.

## Hypothesis
가설 중 하나는 SimSiam이 Expectation-Maximization 알고리즘의 형태를 띌 수 있다는 점임. 
$L(\theta, \eta) = \mathbb{E}_{x,T} \left[ \| F_{\theta}(T(x)) - \eta x \|_2^2 \right]$
F is a network parameterized by θ. T is the augmentation. x is an image. The expectation E[·] is over the distribution of images and augmentations. For the ease of analysis, here we use the mean squared error ∥ · ∥2, which is equivalent to the cosine similarity if the vectors are l2-normalized.

$\min_{\theta, \eta} L(\theta, \eta)$

$\theta^t \leftarrow \arg\min_{\theta} L(\theta, \eta^{t-1})$

$\eta^t \leftarrow \arg\min_{\theta} L(\theta^{t}, \eta)$

하지만 저자들은 충분한 근거를 제시하지 않았으며 모델의 붕괴가 방지되는 현상을 실험적으로 관찰 할 수만 있다고 제시함. 

다음 그림은 여러 unsupervised learning 모델을 간결하게 표시한 도표임,. 
![](/images/Siamese/3.png)


