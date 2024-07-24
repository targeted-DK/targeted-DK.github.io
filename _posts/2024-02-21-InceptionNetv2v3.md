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
- 기본적으로 Feed-forward network은 한 방향으로 정보가 흐르기 때문에 각 레이어 사이에서 정보의 흐름을 확인할 수 있다. 일반적으로 representation 크기는 하류로 갈 수록 서서히 감소해야하는데, 이는 representation의 차원이
- 상관 구조 관계를 내포하고 있기 때문이다. ( Theoretically, information content can not be assessed merely by the dimensionality of the representation as it discards important factors like correlation structure)
- 차원 크기는 정보의 내용만 어림 잡을 수 있게 해준다.

### Higher dimensional representations are easier to process locally within a network. 
- 합성곱 타일(unit) 당 activation 숫자를 증가시키면 feature를 더 자세하게 해석할 수 있다.

### Spatial aggregation 
- 공간 응집(spatial aggregation)dms 저차원 임베딩으로 representation(데이터) 손실 없이 실행할 수 있다. 즉 input representation의 차원을 축소하더라도 각 데이터는 근처 데이터와 유사하기 때문에 출력물을 공간 응집에 사용할 수 있다. 즉 차원 축소는 좀 더 빠른 훈련을 가능하게 한다.

###  Balance the width and depth of the network.
- 연산력을 모델 네트워크의 깊이와 너비에 고루 분포하여 사용해 한다.


## Factorizing Convolutions with Large Filter Size
대부분의 GoogLeNet의 장점은 차원 축소에서 비롯되었고, 차원 축소는 필요 연산력을 감소시키기 때문에 적합한 factorization을 통해 더 압축된 매개 변수와 빠른 훈련에 필요하다. 
Therefore, any reduction in computational cost results in reduced number of parameters. This means that with suitable factorization, we can end up with more disentangled parameters and therefore with faster training. 

### Factorization into smaller convolutions
더 넓은 필터의 크기는 멀리있는 레이어의 signal을 엿볼수 있기 때문에, 필터 크기 감소는 expressiveness(표현력)을 크게 감소시킨다. 다만 이 연구에서는 큰 필터를 여러 레이어의 작은 레이어로 표현하여 연산량을 줄이면서 비슷한 표현력을 구현하려고 한다. 연구에서는 factorization(분해)를 여러 개의 작은 합성곱으로 나누는 것으로 정의한다. 5x5 합성곱은 3x3합성곱 한 개에 비해 연산량이 (3*3)*2/(5*5), 즉 28% 감소한다. 
아래 사진과 같이 결국 두 개의 3x3 레이어는 한 개의 5x5 레이어 구현이 가능하다. 


다음 질문은 과연 이런 차원 축소를 통한 필요 연산량의 감소가 expressiveness 감소를 유발하냐는 질문이다. 이는 output activations(활성화된 출력물)의 batch-normalization을 통해 네트워크가 배우는 이점을 가지게 된다고 생각한다.(We attribute this gain to the enhanced space of variations that the network can learn especially if we batch normalize the output activations.) 

### Spatial Factorization into Asymmetric Convolutions
![](/images/InceptionNetv2v3/2.png)
위에서는 n*n 필터를 더 작은 m*m 필터로 구현하고 했지만, 항상 성능 향상을 보장하지 않아서 이번에는 n*n필터를 1*m 과 m*1필터로 구현하려고 한다. 이런 경우, 기존의 인셉션 모델이 다음과 같이 변형된다. 

![](/images/InceptionNetv2v3/3.png)
![](/images/InceptionNetv2v3/4.png)

In practice, we have found that employing this factorization does not work well on early layers, but it gives very good re- sults on medium grid-sizes (On m × m feature maps, where m ranges between 12 and 20). On that level, very good re- sults can be achieved by using 1 × 7 convolutions followed by 7 × 1 convolutions.

![](/images/InceptionNetv2v3/5.png)
![](/images/InceptionNetv2v3/6.png)

## Utility of Auxiliary Classifiers
we found that auxiliary classifiers did not result in improved convergence early in the training:
Instead, we argue that the auxiliary clas- sifiers act as regularizer.


## Efficient Grid Size Reduction
In order to avoid a  before apply- ing maximum or average pooling the activation dimension of the network filters is expanded.
representational bottleneck(표현 병목)을 피하기위해 먼저 인셉션 모듈을 통과시킨 후 pooling을 적용한다. 
![](/images/InceptionNetv2v3/7.png)

## Inception v2
7x7 합성곱을 3개의 3x3으로 대체하며
e have 3 traditional inception modules at the 35 × 35 with 288 filters each. This is reduced to a 17 × 17 grid with 768 filters using the grid reduction technique described in section 5.
Although our network is 42 layers deep, our computation cost is only about 2.5 higher than that of GoogLeNet and it is still much more efficient than VGGNet.

#### Model Regularization via Label Smoothing

각 데이터 x에 대하여 label k 값을

$$
p(k|x) = \frac{exp(z_{k})}{\sum_{i=1}^{K} exp(z_{i})}

$$













## Conclusion
1. significant quality gain at a modest increase of computational requirements
2. work was competitive despite of neither utilizing context nor performing bounding box regression
3. moving to sparser architectures is feasible and useful idea in general.

## 요약
1. 여러 필터 결과물을 concatenate해서 사용
2. 1x1 합성곱을 통한 차원 축소(=매개 변수 감소)
3. auxiliary classifier를 사용하여 vanishing gredient 줄임
4. VGGNet보다 적은 매개변수 수
