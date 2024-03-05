---
layout: post
title: "Local Features 2"
date: 2024-03-03
categories: ComputerVision
---

## Local Features 2

# Behaviors in regards to image transformations

![](/images/2024-03-03/1.png)

**Image translation/rotation/scaling**
![](/images/2024-03-03/2.png)
![](/images/2024-03-03/3.png)
![](/images/2024-03-03/4.png)
![](/images/2024-03-03/5.png)

# Recap : Harris Corner Detector
![](/images/2024-03-03/6.png)

# Automatic Scale Selection for Interest Point Detection


# Difference of Gaussian vs. the Laplacian of Gaussian (LoG) 

### Laplacian of Gaussian (LoG)

The Laplacian of Gaussian is a two-step process involving the smoothing of an image with a Gaussian filter followed by the application of the Laplacian operator. The Gaussian filter is used to reduce noise and details in the image, making it smoother. The Laplacian operator is then applied to the smoothed image to detect areas of rapid intensity change, which correspond to edges. The LoG operator is essentially a second derivative operator, which means it highlights regions of rapid intensity change more effectively but is also more sensitive to noise. This is why the Gaussian smoothing step is essential.

Mathematically, the LoG operator can be represented as: ∇2G(x,y)\=(∂2∂x2+∂2∂y2)G(x,y)\\nabla^2 G(x, y) = \\left( \\frac{\\partial^2}{\\partial x^2} + \\frac{\\partial^2}{\\partial y^2} \\right) G(x, y)∇2G(x,y)\=(∂x2∂2​+∂y2∂2​)G(x,y) where G(x,y)G(x, y)G(x,y) is the Gaussian smoothing filter applied to the image. The LoG operation is isotropic, meaning it treats all directions of edges equally.

### Difference of Gaussian (DoG)

The Difference of Gaussian is an approximation of the LoG operation that is computationally more efficient and simpler to implement. The DoG is obtained by subtracting one blurred version of an image from another less blurred version of the same image. The blur is achieved with a Gaussian filter, and the difference highlights the edges. The DoG effectively approximates the LoG by taking the difference between two images blurred by Gaussians with different standard deviations.

Mathematically, the DoG operation can be represented as: DoG(x,y)\=G(x,y,kσ)−G(x,y,σ)DoG(x, y) = G(x, y, k\\sigma) - G(x, y, \\sigma)DoG(x,y)\=G(x,y,kσ)−G(x,y,σ) where G(x,y,kσ)G(x, y, k\\sigma)G(x,y,kσ) and G(x,y,σ)G(x, y, \\sigma)G(x,y,σ) are Gaussian functions applied to the image with standard deviations kσk\\sigmakσ and σ\\sigmaσ, respectively.

# SIFT(Scale-invariant feature transform) Detector

The MSER algorithm can be summarized in the following steps:
**Thresholding**: The image is thresholded at multiple levels. For each threshold value, a binary image is generated, where pixels with intensities above the threshold are set to one, and those below are set to zero.

**Component Detection**: For each binary image, connected components are detected. These components represent potential extremal regions.

**Stability Calculation**: The algorithm evaluates the stability of these regions across the different thresholds. A region is considered stable if its size does not change significantly over a range of threshold values.

**Selection of Maximally Stable Regions**: Among the detected extremal regions, those that exhibit maximum stability are selected as features. These regions are invariant to scale and affine transformations, making them useful for matching across different images.

**Feature Extraction and Description**: Optionally, further processing can be applied to describe the selected MSER regions for matching or recognition tasks. Descriptors can be computed to capture the appearance, shape, or other relevant properties of these regions.

![](/images/2024-03-03/7.png)
![](/images/2024-03-03/8.png)
![](/images/2024-03-03/9.png)
![](/images/2024-03-03/10.png)
![](/images/2024-03-03/11.png)


# Maximally Stable Extremal Regions
![](/images/2024-03-03/12.png)
![](/images/2024-03-03/13.png)

# Local Feature Descriptors (SIFT Vector)

![](/images/2024-03-03/14.png)
![](/images/2024-03-03/15.png)
![](/images/2024-03-03/16.png)
![](/images/2024-03-03/17.png)

The Chi-squared $(χ2\\chi^2χ2)$ distance is a statistical measure commonly used in computer vision and image processing to compare histograms, making it particularly useful for tasks like histogram matching, texture analysis, and object recognition. The $χ2\\chi^2χ2$ distance is used to determine how different two histograms are, with a lower distance indicating more similar histograms.

![](/images/2024-03-03/18.png)

The HOG descriptor works by dividing the image into small connected regions, referred to as cells, and for each cell, compiling a histogram of gradient directions or edge orientations for the pixels within the cell. The combination of these histograms then represents the descriptor. Here's a simplified overview of how the HOG descriptor is computed:

**Pre-processing**: Optionally, the image is pre-processed to improve its contrast, which can help in highlighting edge information.

**Gradient Computation**: The first step in computing HOG descriptors is to calculate the gradient magnitude and direction at each pixel in the image. This is typically done using the Sobel operator or a similar edge detection method.

**Cell Histograms**: The image is divided into small spatial regions called cells. For each cell, a histogram of gradient directions is compiled. The gradients of all pixels within the cell are quantized into a number of orientation bins according to their direction, and the magnitude of the gradient contributes to the bin corresponding to its orientation.

**Normalization**: To account for changes in illumination and contrast, the histograms are normalized. This can be done over larger, spatially connected regions called blocks, which overlap with each other. Each cell's histogram can be normalized with respect to the block it's part of, improving the descriptor's robustness to lighting variations.

**Descriptor Assembly**: The normalized histograms of all cells are concatenated to form the final feature vector for the image or region of interest. This feature vector constitutes the HOG descriptor.

# Local Feature Descriptors (SURF)

