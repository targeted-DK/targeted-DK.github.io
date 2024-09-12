---
layout: post
title: "InceptionNet v2 v3"
date: 2024-02-21
categories: DL
---

# InceptionNet v2 v3

## 개요
InceptionNet v2와 v3는 기존 모델을 효율성은 유지하면서 모델을 성능을 강화시키기 위해 향상된 버전이다. InceptionNet v1의 모델 레이어가 복잡하여 모델 그대로 향상 시키기 위해서는(scale up) 더 많은 연산량이 요구된다.
즉 기존 모델의 효율성을 유지하면서 더 많은 데이터를 활용하기에는 무리가 있다. 즉 새로운 버전에서는 모델 향상을 위한 새로운 원칙과 최적화 방법을 제시한다.

## General Design Principles

### Avoid representational bottlenecks, especially early in the network.
- 기본적으로 Feed-forward network은 한 방향으로 정보가 흐르기 때문에 각 레이어 사이에서 정보의 흐름을 확인할 수 있다. 일반적으로 representation 크기는 하류로 갈 수록 서서히 감소해야 하는데, 이는 representation의 차원이
- 상관 구조 관계를 내포하고 있기 때문이다. (Theoretically, information content cannot be assessed merely by the dimensionality of the representation as it discards important factors like correlation structure.)
- 차원 크기는 정보의 내용만 어림잡을 수 있게 해준다.

### Higher dimensional representations are easier to process locally within a network. 
- 합성곱 타일(unit) 당 activation 숫자를 증가시키면 feature를 더 자세하게 해석할 수 있다.

### Spatial aggregation 
- 공간 응집(spatial aggregation)은 저차원 임베딩으로 representation(데이터) 손실 없이 실행할 수 있다. 즉 input representation의 차원을 축소하더라도 각 데이터는 근처 데이터와 유사하기 때문에 출력물을 공간 응집에 사용할 수 있다. 즉 차원 축소는 좀 더 빠른 훈련을 가능하게 한다.

### Balance the width and depth of the network.
- 연산력을 모델 네트워크의 깊이와 너비에 고루 분포하여 사용해야 한다.

## Factorizing Convolutions with Large Filter Size
대부분의 GoogLeNet의 장점은 차원 축소에서 비롯되었고, 차원 축소는 필요 연산력을 감소시키기 때문에 적합한 factorization을 통해 더 압축된 매개 변수와 빠른 훈련이 가능하다.
Any reduction in computational cost results in reduced number of parameters. With suitable factorization, we can end up with more disentangled parameters and faster training.

### Factorization into smaller convolutions
더 넓은 필터의 크기는 멀리 있는 레이어의 signal을 엿볼 수 있기 때문에, 필터 크기 감소는 expressiveness(표현력)을 크게 감소시킨다. 이 연구에서는 큰 필터를 여러 작은 레이어로 표현하여 연산량을 줄이면서 비슷한 표현력을 구현하려고 한다. 연구에서는 factorization(분해)을 여러 개의 작은 합성곱으로 나누는 것으로 정의한다. 5x5 합성곱은 3x3합성곱 한 개에 비해 연산량이 (3*3)*2/(5*5), 즉 28% 감소한다.
아래 사진과 같이 두 개의 3x3 레이어는 한 개의 5x5 레이어 구현이 가능하다.

### Spatial Factorization into Asymmetric Convolutions
![](/images/InceptionNetv2v3/2.png)

nxn 필터를 더 작은 mxm 필터로 구현했지만 항상 성능 향상을 보장하지는 않는다. 이번에는 nxn 필터를 1xm 과 mx1 필터로 구현하려고 한다. 기존 인셉션 모델이 다음과 같이 변형된다.

![](/images/InceptionNetv2v3/3.png)
![](/images/InceptionNetv2v3/4.png)

In practice, this factorization works well on medium grid-sizes (12 to 20 feature maps). On that level, very good results can be achieved by using 1x7 convolutions followed by 7x1 convolutions.

![](/images/InceptionNetv2v3/5.png)
![](/images/InceptionNetv2v3/6.png)

## Utility of Auxiliary Classifiers
We found that auxiliary classifiers did not result in improved convergence early in the training:
Instead, we argue that the auxiliary classifiers act as regularizers.

## Efficient Grid Size Reduction
To avoid representational bottlenecks, we first pass through the inception module, followed by pooling.

![](/images/InceptionNetv2v3/7.png)

## Inception v2
7x7 합성곱을 3개의 3x3으로 대체하며, 42개의 층을 쌓았지만 더 효율적이고 이전 버전과 VGGNet보다 더 적은 연산량을 사용한다.

![](/images/InceptionNetv2v3/9.png)

#### Model Regularization via Label Smoothing

각 데이터 \(x\)에 대하여 label \(k\) 값을

$$
p(k|x) = \frac{\exp(z_{k})}{\sum_{{i=1}}^{K} \exp(z_{i})}
$$

또한 cross-entropy를 사용한 손실 함수는

$$
l = -\sum_{k=1}^{K} \log(p(k))q(k)
$$

Here, \(q(k)\) is the ground-truth label, and the cross-entropy loss is differentiable for training. The gradient is given as:

$$
\frac{\partial l}{\partial z_{k}} = p(k) - q(k)
$$

In this study, label smoothing is applied to reduce model confidence due to one-hot encoding.

#### Label-smoothing regularization (LSR)

$$
y_{i} = (1 - \epsilon)\delta_{i,j} + \frac{\epsilon}{K}
$$

#### Result
![](/images/InceptionNetv2v3/10.png)

The last line of the above graph indicates Inception v3. "BN-auxiliary" refers to the version in which the fully connected layer of the auxiliary classifier is also batch-normalized, not just the convolutions.

## 요약
1. Factorization을 통한 매개 변수 숫자 감소
2. Representational bottleneck을 피하기 위해 바로 pooling을 하지 않고 convolution(s=2)를 사용함. 이것으로 grid size 축소 가능
3. Label smoothing을 사용하여 one-hot encoding에서 오는 '확신'을 줄임
4. Auxiliary classifier 사용 횟수 감소
