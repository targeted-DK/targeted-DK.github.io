---
layout: post
title: "InceptionNet"
date: 2024-02-17
categories: DL
---

# InceptionNet

## 개요
InceptionNet은 "Inception" 모듈을 사용하고 네트워크의 깊이를 증가시키는 방법이다. 기본적인 CNN모델들은 여러 합성곱 층과 정규화(normalization)과 최대 풀링(max pooling), 그리고 완전 연결 층(FC layer)으로
이어지는데, 대용량 데이터일수록 층 개수를 늘리거나, 층의 크기를 늘리거나, dropout를 사용하여 과적합을 방지한다.

### Problems with max-pooling
최대 풀링의 문제점은 주어진 영역(receptive field)의 최댓값을 취함으로써 공간적인 정보를 생략한다는 것이다. 그럼에도 불구하고 인셉션 층을 여러 번 반복하여 22층의 GoogLeNet을 만듬. 

### Network In Network(NIN)
신경망의 표현력을 높이기 위해 rectified linear activation 이후 추가적인 1x1 합성곱 레이어를 추가하는 방식이다(향상된 특징(feature( 추출,  더 많은 비선형성(non-linearity), 파라미터 효율성, 개선된 분류 성능). 이 연구에서는 이 방식을 차용, 차원을 축소하고 계산 병목현상을 줄임. 이는 1) 네트워크에 깊이를 더하고 
2) 연산 패널티 없이 네트워크의 넓이를 증가시킬 수 있음.

### Regions with Convolutional Neural Networks (R-CNN) 
R-CNN의 경우 낮은 레벨에서 특징을 추출한 후 영역을 분리하는 작업을 한 후 (potential object proposals) CNN 분류기를 적용하는 두 단계로 이루어진 CNN임. 이 연구에서는 이 아이디어를 차용하여 여러 영역(multi-box)을 만듬. 

### Problems with general approaches
1) Increasing size -> larger number of parameters, meaning overfitting
2) Dramatically increased use of computational resources. 기본적으로 두 레이어 사이에 필터 갯수의 선형 증가는 계산의 제곱 증가를 의미함. 또한 Vanishing Gradient 문제는 많은 연산을 쓸모 없게 만듬. 

### High-Level solutions to the problem above
1) Dense connection말고 sparse connection을 이용하는 방법. 이는 데이터의 확률 분포를 sparse deep learning network로 표현 할 수 있다는 가정임.
2) 이는 생물학에서 Hebbian principle, 또는 "cells that fire together wire together"로 불리는 현상에서 착안함.
3) 이를 행렬로 나타내면, 여러 sparse 행렬을 군집화하여 생대적으로 dense submatrices로 만듬.(빠른 속도의 계산이 가능)
4) Inception 구조는 sparse 구조를 통해 dense 네트워크를 어림하려는 시도임.

## Architectural Details
1) By using multiple feature extractions using convolutions with different filter sizes(sparsity), the model aims to approximate dense matrics needed for computation by concatenating different filter convolutions. (The merging of the output of the pooling layer with the outputs of convolutional layers would lead to an inevitable
increase in the number of outputs from stage to stage.)
2) Since feature extractions using different convolution filters increases number of parameters dramatically, the paper uses 1x1 convolutions to optimize the number of parameters.
3) Padding is used to solve patch alignment issues.

![](/images/InceptionNet/1.png)





### Input
224x224 크기의 RGB 이미지

### 사용되는 개념
3x3 합성곱 필터 (or receptive field)
1x1 합성곱 필터 (= linear transformation of input channels)
stride by 1 pixel
spatial pooling (five max pooling layers, over 2x2 pixel window with stride = 2). Pooling이 많이 사용되며, 이는 각 layer마다 특징적인 부분(큰 값)을 뽑아내어 다음 레이어로 보내준다고 생각하면 쉽다. 
fully-connected layers
soft-max layers (for classification)

## 개념 설명
 
#### 합성곱(convolution) 사용하는 이유
3x3 합성곱 필터 필터의 패턴(or 특징)으로 주어진 영역의 패턴(or 특징)으로 찾는 연산이다. 여러먼 3x3 합성곱 필터를 거칠 경우 'receptive field'(계산에 포함된 영역)이 넣어진다. 

#### max pooling의 단점
max pooling은 주어진 영역에서 가장 큰 값만 추출하기 때문에 pooling이 계속될 수록 정보가 누락되는 현상이 생김. 누락되는 정보는 특징의 '위치'가 사라진다고 생각하면 쉬움. 예를들어
|0.1|0.3|   ->    |0.9|
|0.4|0.9|
와 같은 2x2 구조에서 0.9만 추출하되고 0.9의 위치적 정보는 사라지기 때문이다. 

#### Soft-max를 마지막에 사용하는 이유
soft-max 함수를 마지막에 사용해서 이미지를 분류할 수 있으며 여러 conv/pooling layer를 통해 추출된 정보를 마지막 레어어에서 한꺼번에 보고 결정을 내릴 수 있기 때문이다.

#### conv layer를 여러 번 사용하는 이유
1. 3x3 레이어를 두번 통과할 경우 5x5를 한번 통과한 효과를 가지며 비선형(non-linearity)를 두번 사용하기 때문에 좀 더 특징을 잘 뽑아낼 수 있다. (makes the decision function more discriminative.)
2. parameter의 숫자가 현저히 줄어드는 효과를 보인다. (3x3 세번 통과 시 : 27C^2, 7x7 한번 통과 시 : 49C^2)

#### 1x1 layer를 사용하는 이유
합성곱 레이어의 receptive field에 변형하지 않고 같은 차원에서 선형 투사(linear projection)를 실행하여 비선형성에 도움을 준다. 

## Architectural design

## 최종 모델 구조
![](/images/VGGnet/1.png)

## 성능
![](/images/VGGnet/2.png)
각 모델 구성(A, B, C ... E) 마다 총 레이어의 개수가 다르며 E의 경우 총 19 레이어를 통과한다.. (16 conv layers + 3 FC layers). 합성곱 레이어의 너비(채널 수)는 첫 번째 레이어에서 64로 시작하여 각 최대 풀링 레이어 이후에 2배로 증가하여 512까지 증가한다. 

Single Test case의 경우 layer가 많을 수록 에러가 줄어드는 점을 확인할 수 있습니다.




