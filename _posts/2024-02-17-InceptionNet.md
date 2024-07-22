---
layout: post
title: "InceptionNet"
date: 2024-02-17
categories: DL
---

# InceptionNet

## 개요
InceptionNet은 "Inception" 모듈을 사용하고 네트워크의 깊이를 증가시키는 방법이다. 기본적인 CNN모델들은 여러 합성곱 층과 정규화(normalization)과 최대 풀링(max pooling), 그리고 완전 연결 층(FC layer)으로 이어지는데, 대용량 데이터일수록 층 개수를 늘리거나, 층의 크기를 늘리거나, dropout를 사용하여 과적합을 방지한다.

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

### High-Level concepts to the problem above
1) Dense connection말고 sparse connection을 이용하는 방법. 이는 데이터의 확률 분포를 sparse deep learning network로 표현 할 수 있다는 가정임.
2) 이는 생물학에서 Hebbian principle, 또는 "cells that fire together wire together"로 불리는 현상에서 착안함.
3) 이를 행렬로 나타내면, 여러 sparse 행렬을 군집화하여 생대적으로 dense submatrices로 만듬.(빠른 속도의 계산이 가능)
4) Inception 구조는 sparse 구조를 통해 dense 네트워크를 어림하려는 시도임.

## Architectural Details
1) By using multiple feature extractions using convolutions with different filter sizes(sparsity), the model aims to approximate dense matrics needed for computation by concatenating different filter convolutions. (The merging of the output of the pooling layer with the outputs of convolutional layers would lead to an inevitable
increase in the number of outputs from stage to stage.)
2) Since feature extractions using different convolution filters increase the number of parameters dramatically, the paper uses 1x1 convolutions to optimize the number of parameters.(That is, 1×1 convolutions are used to compute reductions before the expensive 3×3 and 5×5 convolutions. Besides being used as reductions, they also include the use of rectified linear activation which makes them dual-purpose.)

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
3. 완전 연결층(FC layer)의 경우 1024의 개체가 사용되었으며 ReLu가 사용됨. A fully connected layer with 1024 units and rectified linear activation.
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
1. significant quality gain at a modest in- crease of computational requirements
2. work was competitive despite of neither utilizing context nor performing bounding box regression
3. moving to sparser architectures is feasible and useful idea in general.

