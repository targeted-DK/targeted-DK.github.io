---
layout: post
title: "Robust Fitting"
date: 2024-03-25
categories: ComputerVision
---

# Robust Fitting

![](/images/2024-03-25/1.png)
![](/images/2024-03-25/2.png)

# Fitting : Overview
1) Least squares line fitting
2) Robust fitting
3) RANSAC
4) Hough Transform


## Least Squares Line Fitting
![](/images/2024-03-25/3.png)

Two big problems - Not equivariant to rotation and slope intercept parametrization fails for vertical lines.
So, we end up using Total least squares : 

![](/images/2024-03-25/4.png)
As we rotate the system, the residual (distance between point and slope) stays the same.
![](/images/2024-03-25/5.png)

## RANSAC (Random Sample Consensus)
-> very general framework for model fitting in the presence of outliers
Outline:
  Randomly choose a small initial subset of points
  Fit a model to that subset
  Find all inlier points that are “close” to the model and reject the rest as outliers
  Do this many times and choose the model with the most inliers
Repeat 𝑁 times:
  Draw 𝑠 points uniformly at random
  Fit model to these 𝑠 points
  Find inliers to the model among the remaining points (points whose distance or residual w.r.t. model is less than 𝑡)
  If there are 𝑑 or more inliers, accept the model and refit using all inliers

![](/images/2024-03-25/6.png)
![](/images/2024-03-25/7.png)
![](/images/2024-03-25/8.png)

## Hough Transform
![](/images/2024-03-25/9.png)
![](/images/2024-03-25/10.png)
![](/images/2024-03-25/11.png)
![](/images/2024-03-25/12.png)
![](/images/2024-03-25/13.png)
![](/images/2024-03-25/14.png)
![](/images/2024-03-25/15.png)






