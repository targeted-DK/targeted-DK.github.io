---
layout: post
title: "Pixel-Perfect Structure-from-Motion with Featuremetric Refinement"
date: 2024-09-16
categories: papers
---

## Pixel-Perfect Structure-from-Motion with Featuremetric Refinement

## Different feature descriptors

### SIFT
1. Keypoint detection : uses difference of Gaussians(DoGs)
- D(x,y,σ)=(G(x,y,kσ)−G(x,y,σ))∗I(x,y)
- Function G indicates gaussian blur, and σ indicates different scales. I(x,y) indicates the image.

2. Keypoint localization (points with extreme values)
- $X^2 = -H^{-1}∇D$
- H is the Hessian matrix, and  ∇D indicates the gradient of the difference of Gaussians. 


### SURF
- Faster than SIFT, while still providing robustness to scale and rotation.
- Approximation of SIFT’s steps.
- Uses Haar wavelets for faster computation.

1. Keypoint detection : 
![](/images/Structure-from-Motion/1.png)


### ORB
- Extremely fast and efficient, suitable for real-time applications.
- Binary descriptor rather than floating-point (as in SIFT/SURF).
- Combines FAST keypoint detection with a BRIEF descriptor.
- 
1. Keypoint detection :
-$I(x_{i} > I(p) + t  or  I(x_{i}) < I(p) - t

### Orientation Assignment
SIFT: Uses a **gradient orientation histogram** to assign the dominant orientation to each keypoint, ensuring rotation invariance.
SURF: Uses **Haar wavelet** responses to determine the dominant orientation, then aligns the descriptor accordingly.
ORB: Uses the **intensity centroid method** to compute the keypoint's orientation and ensures rotation invariance by aligning the BRIEF descriptor to this orientation.


## Bundle
The term bundle comes from the bundle of rays that extend from a camera to the points in a scene. These rays represent the projection of 3D points in the world onto the 2D image plane of the camera.
Each 3D point in the scene is projected onto the image plane of each camera that observes it.
The bundle refers to the collection of these rays (from multiple cameras) that intersect at each 3D point.
Bundle adjustment refines these estimates to ensure the projections of the 3D points onto the camera images closely match the detected keypoints, thus improving the overall reconstruction accuracy.



