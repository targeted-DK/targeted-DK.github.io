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
1.  **Compute the Gradient**: Calculate the x and y derivatives of the image to get two images which represent changes in intensity in both directions.
    
2.  **Compute the Structure Tensor**: For each pixel in the image, a 2x2 matrix (also known as the Harris Matrix) is computed, which contains sums of products of derivatives at each pixel. The matrix is given by:
    
    M\=∑x,yw(x,y)\[Ix2IxIyIxIyIy2\]M = \\sum\_{x,y} w(x, y) \\begin{bmatrix} I\_x^2 & I\_xI\_y \\\\ I\_xI\_y & I\_y^2 \\end{bmatrix}M\=x,y∑​w(x,y)\[Ix2​Ix​Iy​​Ix​Iy​Iy2​​\]
    
    where IxI\_xIx​ and IyI\_yIy​ are the x and y derivatives of the image, w(x,y)w(x, y)w(x,y) is a window function (often a Gaussian) that gives weights to pixels around the central pixel.
    
3.  **Response Calculation**: For each pixel, calculate the response function RRR using the determinant and trace of the matrix MMM, given by:
    
    R\=det(M)−k⋅(trace(M))2R = \\text{det}(M) - k \\cdot (\\text{trace}(M))^2R\=det(M)−k⋅(trace(M))2
    
    where det(M)\=λ1λ2\\text{det}(M) = \\lambda\_1 \\lambda\_2det(M)\=λ1​λ2​ (product of the eigenvalues of MMM), trace(M)\=λ1+λ2\\text{trace}(M) = \\lambda\_1 + \\lambda\_2trace(M)\=λ1​+λ2​, and kkk is a sensitivity factor, typically around 0.04 to 0.06.
    
4.  **Thresholding and Non-Maximum Suppression**: Apply a threshold to RRR to determine potential corners. To get a precise localization of corners, non-maximum suppression is applied, which means keeping locations with a local maximum of RRR and discarding all others.
    

Significance and Usage

The Harris Corner Detector is widely used in image processing, computer vision, and related fields for feature detection. Corners detected by this method can be used for various applications, such as image matching, motion detection, and tracking, 3D modeling, and object recognition.

One of the key advantages of the Harris Corner Detector is its invariance to rotation, which makes it robust in identifying corners regardless of the image orientation. However, it is not entirely scale-invariant, meaning it might not detect the same corners if the image size changes significantly. To address this, variations and extensions of the original algorithm, such as the Harris-Laplace and Harris-Affine detectors, have been developed to include scale invariance.




