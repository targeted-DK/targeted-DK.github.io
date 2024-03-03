---
layout: post
title: "Local Features"
date: 2024-02-22
categories: ComputerVision
---

## Local Features

# Correspondence Estimation
Is this point in this image the same as the other image? This is used for recognition, geometry estimations etc.

![](/images/2024-02-22/1.png)
![](/images/2024-02-22/2.png)
![](/images/2024-02-22/3.png)
![](/images/2024-02-22/4.png)
![](/images/2024-02-22/5.png)
![](/images/2024-02-22/6.png)
![](/images/2024-02-22/7.png)


# Gaussian filters in noisy edges
Use Gaussian on errors, by using 'separability of Gaussian'

![](/images/2024-02-22/8.png)
![](/images/2024-02-22/9.png)
![](/images/2024-02-22/10.png)
![](/images/2024-02-22/11.png)
![](/images/2024-02-22/12.png)
![](/images/2024-02-22/13.png)
![](/images/2024-02-22/14.png)
![](/images/2024-02-22/15.png)


# Canny Edge Detection - (Non-maximum suppression + Hysteresis thresholding)

Non-maximum suppression : Non-maximum suppression (NMS) is a technique used in various computer vision and image processing applications, particularly in object detection tasks. The primary goal of NMS is to eliminate redundant bounding boxes that are likely to cover the same object in an image, leaving only the most probable bounding box for each object. This is an essential step in object detection workflows, especially those involving multiple detections around a single object, such as in face detection, pedestrian detection, or vehicle detection in autonomous driving systems.


![](/images/2024-02-22/16.png)
![](/images/2024-02-22/17.png)


Hysteresis thresholding is a technique used primarily in image processing to detect edges in images. It's an **advanced method that applies two different threshold values** to separate pixels into three categories: strong, weak, and non-relevant. This technique is part of the Canny edge detection algorithm, known for its effectiveness in identifying the edges of objects within an image.
![](/images/2024-02-22/18.png)
![](/images/2024-02-22/19.png)
![](/images/2024-02-22/20.png)
![](/images/2024-02-22/21.png)
![](/images/2024-02-22/22.png)

# Keypoint Matching

![](/images/2024-02-22/23.png)
![](/images/2024-02-22/24.png)
![](/images/2024-02-22/25.png)
![](/images/2024-02-22/26.png)
![](/images/2024-02-22/27.png)
![](/images/2024-02-22/28.png)
![](/images/2024-02-22/29.png)


# Harris Corner Detector
The Harris Corner Detector is a popular method used in computer vision to detect corners within an image. It was introduced by Chris Harris and Mike Stephens in their paper "A Combined Corner and Edge Detector" in 1988. The algorithm identifies regions in an image that have significant changes in intensity in all directions, which are typically indicative of corners.

![](/images/2024-02-22/30.png)
![](/images/2024-02-22/31.png)
![](/images/2024-02-22/32.png)
![](/images/2024-02-22/33.png)
![](/images/2024-02-22/34.png)
![](/images/2024-02-22/35.png)
![](/images/2024-02-22/36.png)
![](/images/2024-02-22/37.png)
![](/images/2024-02-22/38.png)
![](/images/2024-02-22/39.png)
The use of inverse root of eigenvalues for the axis lengths of the ellipse provides a geometric interpretation of the gradient information encoded in the second moment matrix, illustrating the rate of intensity change around a point in an image. This interpretation is particularly useful in corner detection, as corners will have relatively balanced, high values for both eigenvalues, resulting in a more circular ellipse (indicating significant intensity changes in all directions).
$R\=det(M)−k(trace(M))$
*   det(M) is the determinant of the second moment matrix M, which can also be expressed as $λ1λ2\\lambda\_1 \\lambda\_2λ1​λ2​$ (the product of the eigenvalues).
*   $trace(M)\\text{trace}(M)trace(M)$ is the trace of the matrix M, which is the sum of its eigenvalues $λ1+λ2\\lambda\_1 + \\lambda\_2λ1​+λ2$​.
*   k is an empirically determined sensitivity factor, usually in the range of 0.04 to 0.06.

The response function RRR has the following interpretations based on the eigenvalues:

*   If both $λ1\\lambda\_1λ1$​ and $λ2\\lambda\_2λ2​$ are small, which means the gradients are small in all directions, R will be small, and the pixel is likely to be part of a flat region.
*   If one eigenvalue is high and the other is low, RRR will also be low, indicating an edge.
*   If both $λ1\\lambda\_1λ1$​ and $λ2\\lambda\_2λ2$​ are high, and hence $det⁡(M)$ is high and $trace(M)$ is moderate, R will be high, indicating a corner.


![](/images/2024-02-22/40.png)
![](/images/2024-02-22/41.png)

The corner response function R is calculated for every pixel in the image, and it is based on the eigenvalues of the second moment matrix M (also known as the autocorrelation matrix or the Harris matrix) for that pixel. The second moment matrix is derived from the gradients of the image, which capture the intensity changes in the local neighborhood of the pixel.


![](/images/2024-02-22/42.png)
![](/images/2024-02-22/43.png)
