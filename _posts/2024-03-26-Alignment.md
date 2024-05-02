---
layout: post
title: "Image Alignment"
date: 2024-03-26
categories: ComputerVision
---

# Image Alignment

![](/images/2024-03-26/1.png)
## Overview
1. Fitting of transformations
  Affine transformations
  Homographies
2. Robust alignment 
  Descriptor-based feature matching
  RANSAC
3. Large-scale alignment
  Inverted indexing
  Vocabulary trees

Examples of image alignment : 
![](/images/2024-03-26/2.png)
![](/images/2024-03-26/3.png)
![](/images/2024-03-26/4.png)
![](/images/2024-03-26/5.png)

## Feature-based alignment
![](/images/2024-03-26/6.png)

Alignment examples:
- Upsampling (Bilinear interpolation)
  Downsampling
![](/images/2024-03-26/7.png)

Examples of interpolations : 
![](/images/2024-03-26/8.png)

Image warping
![](/images/2024-03-26/9.png)

Calculations for Image warping
![](/images/2024-03-26/10.png)
![](/images/2024-03-26/11.png)
![](/images/2024-03-26/12.png)
![](/images/2024-03-26/13.png)
![](/images/2024-03-26/14.png)

### Rotations
![](/images/2024-03-26/15.png)
![](/images/2024-03-26/16.png)
![](/images/2024-03-26/17.png)

### Affine transformations
![](/images/2024-03-26/18.png)

### Projective Homography
![](/images/2024-03-26/19.png)


### Forward warping
![](/images/2024-03-26/20.png)

### Inverse warping
![](/images/2024-03-26/21.png)

Which is better? (Forward vs. inverse warping)
Usually inverse: more efficient, doesn’t have a problem with holes
However, it requires an invertible warp function, which is not always possible


## Fitting of Transformations

Basic Idea:
![](/images/2024-03-26/22.png)

Models (as described in image warpings)
![](/images/2024-03-26/23.png)

### Affine transformations
Simple fitting procedure: linear least squares

![](/images/2024-03-26/24.png)
![](/images/2024-03-26/25.png)
![](/images/2024-03-26/26.png)


### Projective Homography
![](/images/2024-03-26/27.png)
![](/images/2024-03-26/28.png)
![](/images/2024-03-26/29.png)
![](/images/2024-03-26/30.png)
![](/images/2024-03-26/31.png)

## Robust Alignment
### Robust feature-based alignment
![](/images/2024-03-26/32.png)

### RANSAC
![](/images/2024-03-26/33.png)
![](/images/2024-03-26/34.png)



### Vocabulary tree (yet to be reviewed)

