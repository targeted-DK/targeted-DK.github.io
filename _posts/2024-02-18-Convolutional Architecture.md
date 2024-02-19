---
layout: post
title: "Convolutional Architecture"
date: 2024-02-18
categories: Computer Vision
---

## Convolutional Architecture (From WashU CV Lecture 9 by Nathan Jacobs)

Important Terms :
- Three-dimensional convolutions
  What if the input to a convolutional layer is a stack of K (Height x Width x K) filter and L filters. Assuming the input feature maps have a spatial resolution of HxW, and the filter has a size of FxF, we need total operations of $F^2$KLHW to compute the output feature volume. 
![](/images/2024-02-18/02.png)

- 1x1 convolutional layer
  Instead of the filter being FxF, consider a 1x1 filter. (F = 1). Using a filter means extracting spatial features and mixing them across the channels. Therefore, 1x1 convolution purely mixes things across channels without extracting spatial features.
![](/images/2024-02-18/04.png)
The use of a 1x1 layer reduces costs and allows us to use less number of weights.

- Depthwise convolutions
![](/images/2024-02-18/05.png)
This is a description of one channel in, and one channel out. This cannot mix across channels and we are just extracting spatial features. We have separate filters for every input layer, therefore learning features of each layer. Due to the nature of Depthwise convolutions, we can use this with 1D convolution to mix across channels. Using depthwise with 1D convolution drastically decreases total number of operations needed as indicated by the slide below :
![](/images/2024-02-18/06.png)

- Groupwise convolutions
![](/images/2024-02-18/07.png)

- 3D to 2D convolutions
![](/images/2024-02-18/08.png)


**Bold Text** and *Italic Text*.
