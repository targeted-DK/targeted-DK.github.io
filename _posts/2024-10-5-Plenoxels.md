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
custom CUDA를 사용하여 모델의 간소함을 응용해 매우 빠른 속도를 보이며, Titan RTX GPU하나 만으로 4일 걸리는 NeRF작업을 11~27분 사이로 단축시켰다. Fast rendering에는 아직 최적화되어이/ㅆ진 않지만
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
### Classic Volum Reconstruction
Volume rendering에 사용되는 가장 기본적인 방법은 voxel grid과 multiplane images의 사용이다. Voxel grid는 임의의 위상을 표현할 수 있지만 해상도가 높아질 수록 사용이 제한적이다. 메모리 사용을 줄이기 위해서는 여러 hierarchial 구조를 사용하는 방법이 있다. 예를들어
octree를 사용하거나 더 간단한 sparse array 구조를 사용하는 방법이 있다.

![](/images/Siamese/3.png)


