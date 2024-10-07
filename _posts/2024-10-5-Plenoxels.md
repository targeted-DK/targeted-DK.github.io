---
layout: post
title: "Plenoxels: Radiance Fields without Neural Networks"
date: 2024-10-05
categories: papers
---

# Plenoxels: Radiance Fields without Neural Networks
 
## Overview
이 논문은 Plenoxels (plenoptic voxels)를 이용하여 photorealstic view 합성 시스템을 보여준다. Plenoxel는 어떤 장면을 sparse 3d grid와 spherical harmonics로 표현한다.
이는 여러 최적화 방법으로 계량된 이미지로부터 표현이 가능하며, neural components를 사용하지 않는다. 그럼에도 불구하고 Plenoxels는 NeRF보다 100배 이상 빠르게 계산할 수 있음을 보여준다. 

NeRF는 매우 calibrated된 image로부터 3D volume을 좌표 기반 neutral network를 사용하여 scene geometry, view-dependent effect와 매우 photorealistic한 viewpoint rendering을 가능하게 함. 
하지만 높은 퀄리티에도 불구하고 단점이 존재하는데, 이는 훈련과 렌더링에 필요한 연산량과 시간이마. 30fps를 하나의 gpu로 렌더링 할 시 하루 이상이 걸리게 된다. 여러 연구에서는 렌더링에 필요한 연산 비용을 줄이려고 하였지만,
gpu 한 개로는 아직까지 여러 시간이 걸리며 이는 실제 photorealistic volumetric reconstruction을 현실에 응용하기에 병목이 되고있다.

이 연구에서는 neural network 없이 radiance field를 훈련하는 방법을 소개한다. 그럼에도 불구하고 NeRF 퀄리티에 부족하지 않고 최적화 시간을 100배 이상줄이는 방법을 소개한다.
custom CUDA를 사용하여 모델의 간소함을 응용해 매우 빠른 속도를 보이며, Titan RTX GPU하나 만으로 4일 걸리는 NeRF작업을 11~27분 사이로 단축시켰다. Fast rendering에는 아직 최적화되어있진 않지만
새로운 viewpoint를 15fps로 렌더링 할 수 있다. 

특히 이 연구에서는 expllicit volumetric representation을 사용하며 calibrated 2d 이미지로 end-to-end 모델을 사용하여 photorealistic novel vewipoints를 렌더링 가능하며,
이는 미분가능한 렌더링 손실과 합산 variation regularization(픽셀에 smoothing 적용) 으로 가능하다.

Plenoxel는 plenoptic volument element의 준말이며, sparse voxel grid를 사용하면서도 opacity와 spherical harmoic coefficient를 저장한다. 이런 계수들은 모델의 interpolation에 사용되어 
연속공간 함수에 사용된다.

1개 gpu를 사용하여 고해상도 결과물을 얻기 위하여 빈 voxel를 잘라내는 최적화 전략을 사용한다. 물론 모델 자체가 voxel grid를 사용하긴 하지만, 모델이 unbounded 장면에도 사용되도록 설계하였다.
이는 정규화된 좌표를 사용하고 좌표계를 multisphere image로 감싸 배경을 인코딩하도록 가능하게 했다.

이 연구의 모델은 여로 관측된 결과물(inverse problems)로 photorealistic volumetric reconstruction를 가능하게 한다. (data representation, forward model, regularization function, optimizer 사용)
즉, NeRF의 핵심은 neural network의 사용이 아닌 미분가능한 volumetric renderer이다.

## 관련 연구
### Classic Volume Reconstruction
Volume rendering에 사용되는 가장 기본적인 방법은 voxel grid과 multiplane images의 사용이다. Voxel grid는 임의의 위상을 표현할 수 있지만 해상도가 높아질 수록 사용이 제한적이다. 메모리 사용을 줄이기 위해서는 여러 hierarchial 구조를 사용하는 방법이 있다. 예를 들어 octree를 사용하거나 더 간단한 sparse array 구조를 사용하는 방법이 있다. 좌표를 기반으로한 표현 방식과 보간법(interpolation)를 사용하여 연속적인 표현을 나타낼 수 있으며 표준 신호 처리 방식을 통해 resize가 가능하다. 즉 sparsity와 보간법을 조합하여 더 간단한
좌표 기반 모델이 3D 장면을 고해상도로, 메모리 제한 없이 표현을 가능하게 한다. 이 연구에서는 이러한 고전적 방법과 forward volume rendering formula를 사용하여 간접적인 2d 관찰로부터 directly하게 3d 모델을 최적화하도록 설계하였다. 또한 이런 고전적 방식에서 벗어나 view dependence 기능을 추가하였는데, 이는 각 voxel의 color channel의 spherical harmonic coefficient 최적화하여 가능하게 했다. 

### Neural Volume Reconstruction
Neural implicit representation은 모델의 occupancy, signed distance to an object's surface, perform novel view synthesis from 3D point cloud를 가능하도록 한다. 
또한 NeRF는 미분가능한 volume rendering공식을 사용하여 좌표 기반 다중 퍼셉트론을 사용하며 이는 직접적으로 3d 위치와 2d 보는 방향으로부터 색과 opacity를 훈련한다. 결과물 자체는 좋지만 훈련 시간이 길다. 이는 각 픽셀에 수백 개의 샘플 location의 ray를 사용하여 좌표 기반 다중 퍼셉트론을 사용해야 하기 때문이다. 하지만 많은 연구들이 NeRF를 보완하여 360 장면 모델링이나 다중 렌더링에서의 안티 엘리어싱 사용 등 여러 기법을 추가적으로 도입하였다.

이 연구와 가장 비슷한 방법은 Neural Volumes이다. NV는 voxel grid와 보간법을 사용하지만, CNN을 통해여 좌표를 최적화한다. 하지만 이 연구에서는 voxel grid 최적화를 pruning과
coarse to fine 최적화를 통해 시도하였으며, 이는 neural network 또는 warping function을 배제하였다.

### Accelerating NeRF
NeRF의 훈련시간을 가속화하기 위한 여러 방법중에서 본 연구에 사용된 방법은 PlenOctree이다. 특히 여기서는 spherical harmoninc coefficients를 사용하는데, 이 아이디어를 차용하여 좀 더 일반화된 sparse plenoptic voxel grids에 적용가능하게 하고 trilinear interpolation을 사용하였다.(sparse voxel structure에 사용하기 쉬움).
![](/images/Plenoxels/1.png)


## Method
이 모델은 기본적으로 sparse voxel grid이며, occupied된 voxel corner에는 scalar opacity $\delta$와 각 컬러 채널의 spherical harmonic 계수 벡터가 저장된다. 이를 우리는 **Plenoxel** 이라고 명명한다. 임의 위치의 불투명성과 색, 그리고 관찰 방향은 근처 픽셀의 삼선 보간법을 이용해 구면 고조파를 계산하는데 있다. 주어진 수정된 이미지를 통해 훈련용 광선의 렌더링 손실을 사용하여 모델을 최적화할 수 있다. 

### Volume Rendering
기본적으로 우리는 NeRF와 같은 미분 가능한 volume rendering 모델을 사용한다. 이는 광선의 색이 광선 상에 위치한 샘플을 모두 합 한 값이 된다. 
![](/images/Plenoxels/2.png)

$T_{i}$는 광선 **r**에 얼마나 많은 빛이 샘플 *i*를 통해 투과되어있는지 나타내며, $1 - exp($\sigma_{i}\delta_{i})$는 각 샘플 *i*의 빛의 양, $\sigma_{i}$는 각 샘플 *i*의 불투명성, 그리고 **$c_{i}**는 각 샘플 *i*의 색깔과 $\delta_{i}$는 다음 샘플과의 거리를 나타낸다. 

### Voxel Grid with Spherical Harmonics
자료 구조를 간소화 하기 위해 octree보다는 dense 3D index array에 포함된 포인터를 occupied voxel의 값을 포함한 분리된 자료 배열로 저장한다. 각 occupied voxel은 scalar opacity $\delta$와 각 컬러 채널의 spherical harmonic coefficient가 포함된 벡터를 저장한다. Spherical harmonics는 구를 정의하는 직교 기저를 구성하며, 이는 low degree harmonics가 색의 부드러운 변화를 인코딩하며 higher degree harmonics는 higher-frequency의 변화를 인코딩한다. 샘플의 색인 **c_{i}$**은 각 컬러 채널의 harmonic basis function의 합이며, 주어진 관찰 방향에 따라 최적화된 계수(가중치)가 적용된다. 구면 조화의 차수는 2이며, 이는 각 색상 당 9개의 계수를 필요로 하며, 각 voxel당 총 27개의 조화 계수가 필요하게 된다. 이 연구에서는 조화 차수 2를 사용하는데 이는 PlenOctrees연구에서 차수를 증가시켜도 많은 이득을 찾지 못했기 때문이다.

즉 Plenoxel 좌표는 3선 보간법을 사용하여 연속 plenoptic 함수를 사용해 부피를 정의한다. 이는 PlenOctrees에서는 불투명성과 구면 조화 계수가 각 voxel에서 상수로 유지된다고 가정하는 것과는 완전 반대이다. 이 차이점으로 인해 부피를 성공적으로 최적화하는데 가장 중요한 요소로 작용한다. 모든 불투명성 및 구면 조화 계수는 직접적으로 최적화가 가능하며, 특별한 초기화나 neural network를 이용한 pretraining을 사용하지 않아도 된다.

### Interpolation
각 광선에 위치한 샘플 포인트의 불투명성과 색은 가장가까운 8개 voxel의 불투명성과 조화 계수를 이용한 삼선 보간법을 이용해 계산할 수 있다. 3선 보건법은 간단한 근접 이웃 보간법 보다 훨씬 빠르다. 보간법은 좀 더 해상도를 증가시키고 연속 함수의 어림을 가능하게하여 최적화를 가능하게 한다. 
![](/images/Plenoxels/3.png)

### Coarse to Fine
Coarse-to-fine strategy를 사용하여 dense grid를 이용한 저해상도에서 시작하여, 필요없는 voxel를 잘라내어 최적화하며 남아있는 voxel들을 각 차원에서 반으로 나눈 다음 계속 최적화하는 방식이다. 예를 들어, 256 해상도에서 시작하여 512 해상도로 업샘플링을 할 수 있다. voxel을 반으로 나누는 과정 이후 삼선 보간법을 사용하여 각 좌표 값을 초기화한다. 임의의 해상도로 시작하여 삼선 보간법을 이용하여 크기를 조절할 수도 있다. 다운샘플링 시 좌표계의 복잡성을 줄이는 과정은 Voxel를 잘라내는 과정은 PlenOctrees의 방법을 차용하는데, 이는 training rays 상에 최대 가중치를 적용하여( ($T_{i}(1-\exp(-\sigma_{i}\delta_{i}) 또는 $\sigma_{i}$ ) 수행한다.

### Optimization
이 연구에서는 voxel의 불투명성과 구체 조화 계수를 평균 제곱 오차와 total variation regularization을 이용하여 최적화에 사용한다. 
![](/images/Plenoxels/4.png)

