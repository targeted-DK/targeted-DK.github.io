---
layout: post
title: "Architectures"
date: 2024-04-11
categories: ComputerVision
---



# RetinaNet
RetinaNet is a popular and effective deep learning framework for object detection, introduced by Facebook AI Research (FAIR) in a 2017 paper titled "Focal Loss for Dense Object Detection." It addresses one of the central challenges in object detection: the imbalance between foreground and background classes during training, particularly when dealing with extreme cases where background (negative) examples vastly outnumber the foreground (positive) examples.

- Combine feature pyramid network with focal loss to reduce the standard cross-entropy loss for well-classified examples
- Focal loss is designed to focus training on a sparse set of hard examples and prevents the vast number of easy negatives from overwhelming the detector during training.

# CornerNet
CornerNet is a type of convolutional neural network designed for object detection that fundamentally changes the traditional approach of using bounding boxes predicted from anchor boxes. Introduced in a 2018 paper titled "CornerNet: Detecting Objects as Paired Keypoints" by Hei Law and Jia Deng, CornerNet proposes a novel method for detecting objects by identifying their corners (top-left and bottom-right) as a pair of keypoints.

- Keypoint detection : detects objects by locating their corners. Uses CNN to predict heatmets for top left and bottom right corners of objects.

# Transformer

# Self Attention layer

The term "self-attention layer" specifically refers to the mechanism within the layer that enables each part of the input (like each word in a sentence) to attend to—or consider—all other parts of the input when processing its own representation. It's named "self-attention" because it involves the input sequence attending to itself, rather than focusing on some external input or sequence.

Here’s a breakdown of why the name fits the mechanism:

Self: This part of the name emphasizes that the attention mechanism is focused on the same sequence that it is part of. In other words, each element of the sequence (e.g., each word in the sentence) computes its attention based on all elements in the same sequence—including itself. It does not look outside the sequence or at an independent input.

Attention: This refers to how the mechanism selectively focuses on certain parts of the input sequence over others. By computing how much focus or weight to assign to each part of the sequence, the attention mechanism allows the model to dynamically highlight and integrate information from different parts of the input.  

How Self-Attention Works:
Each element in the input generates three vectors through linear transformation: a query vector, a key vector, and a value vector.
The attention score between any two elements is computed by taking the dot product of the query vector of one element with the key vector of the other. This score determines how much one element should attend to another.
These scores are then normalized using a softmax function, which converts them into a probability distribution (weights) that sums to one.
The output for each element is a weighted sum of the value vectors, where the weights are derived from the attention scores. This means each output element is a composite of information from other parts of the sequence, emphasizing elements that are deemed more relevant.
The term "self-attention" succinctly captures the essence of this process, where each part of the input is actively involved in determining how it should integrate information from every other part of the same input, effectively allowing the sequence to self-regulate the flow of information based on its own content.



# Vision Transformer
