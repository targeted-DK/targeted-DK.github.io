---
layout: post
title: "Convolutional Architecture"
date: 2024-02-18
categories: Computer Vision
---

## Convolutional Architecture (From WashU CV Lecture 9 by Nathan Jacobs)

Important Terms :
- Three-dimensional convolutions
  What if the input to a convolutional layer is a stack of K maps? (Height x Width x K) filter and L filters. Assuming the input feature maps have a spatial resolution of HxW, and the filter has a size of FxF, we need total operations of $F^2$KLHW to compute the output feature volume. 
![](/images/2024-02-18-02.png)


**Bold Text** and *Italic Text*.
