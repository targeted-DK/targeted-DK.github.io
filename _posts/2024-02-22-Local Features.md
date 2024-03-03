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

