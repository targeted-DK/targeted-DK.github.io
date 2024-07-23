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
- 상관 구조 관계를 내포하고 있기 때문이다. ( Theoretically, infor- mation content can not be assessed merely by the di- mensionality of the representation as it discards important factors like correlation structure)
- 차원 크기는 정보의 내용만 어림 잡을 수 있게 해준다.

### Higher dimensional representations are easier to process locally within a network. 
- 합성곱 타일 당 activation 숫자를 증가시키면 feature를 더 자세하게 해석할 수 있다.

### Spatial aggregation 
- 공간 응집(spatial aggregation)는 저차원 임베딩으로 representation(데이터) 손실 없이 실행할 수 있다. 즉 input representation의 차원을 축소하더라도 각 데이터는 근처 데이터와 유사하기 때문에 출력물을 공간 응집에
- 사용할 수 있다. 즉 차원 축소는 좀 더 빠른 훈련을 가능하게 한다.

###  Balance the width and depth of the network.
- 연산력을 모델 네트워크의 깊이와 너비에 고루 분포하여 사용해 한다.


## Factorizing Convolutions with Large Filter Size
대부분의 GoogLeNet의 장점은 차원축소에서 비롯되었다. 


![](/images/InceptionNet/1.png)


![](/images/InceptionNet/2.png)
- 각 인셉션 모듈의 output size는 1x1, 3x3, 5x5, pool proj의 갯수를 더한 값이 된다. 

### Fully Connected Layer(FC) vs Global Average Pooling(GAP)
![https://www.researchgate.net/figure/Fully-flattened-layer-and-global-pooling-layer-The-step-next-to-encoding-is-to-generate_fig4_364679555](/images/InceptionNet/3.png)


![](/images/InceptionNet/4.png)
![](/images/InceptionNet/5.png)
- 여러 인셉션 모듈이 이어져있으며, 길목 마다 max-pooling이 존재한다. 예전에는 Local Reponse Normalization(LRN)을 사용했지만 현재는 conv-batch-relu로 이어지는 레이어를 사용한다.

![](/images/InceptionNet/6.png)
- auxiliary classifiers가 추가됨. (vanishing gradient 방지). 마지막 계싼되는 softmax의 loss = out_loss + 0.3*(aux2_loss + aux3_loss)
- By adding auxiliary classifiers connected to these intermediate layers, we would expect to encourage discrimination in the lower stages in the classifier, increase the gradient signal that gets propagated back, and provide additional regularization

### layer configurations
![](/images/InceptionNet/7.png)
1. average pooling으로는 5x5 필터와 stride 값은 3을 사용하여 위 표에 나와았는 4a, 4d 단계에 각각 4×4×512, 4×4×528이 출력됨.
2. 1x1 합성곱으로 128개의 필터가 차원축소(dimension reduction)에 사용되었으며 ReLu가 사용됨.
3. 완전 연결층(FC layer)의 경우 1024의 개체가 사용되었으며 ReLu가 사용됨.
4. Dropout 비율은 79%.
5. Softmax loss is used for loss calculation.

## Model Performance
![](/images/InceptionNet/8.png)
- 7 models were ensembled(6 models with same initialization + 1 wider model). The only difference was the order of images being trained.
- Resize the image to 4 scales where the shorter dimension (height or width) is 256, 288, 320 and 352 respectively, take the left, center and right square of these resized images 
- 간단히 말하면, 이미지를 4개로 resize (make whatever is shorter of height and width to match 256 or 288 or 320 or 352) and crop into three images (left, center, right or top, center, bottom
- 좌우 반전까지 포함.
- 다음 각 3개의 이미지를 또 4개 코너와 중앙 이미지로 총 5개를 224x224 사이즈로 crop.
- 4*3*2(mirrored)*6(4+1+1(original image resized to 224x224) = 144 images

## Conclusion
1. significant quality gain at a modest increase of computational requirements
2. work was competitive despite of neither utilizing context nor performing bounding box regression
3. moving to sparser architectures is feasible and useful idea in general.

## 요약
1. 여러 필터 결과물을 concatenate해서 사용
2. 1x1 합성곱을 통한 차원 축소(=매개 변수 감소)
3. auxiliary classifier를 사용하여 vanishing gredient 줄임
4. VGGNet보다 적은 매개변수 수
