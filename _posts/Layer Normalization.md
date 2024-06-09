---
layout: post
title: "[Paper] Layer Normalization"
date: 2024-06-09
categories: papers
---

## Abstract
  - Deep learning is computationally expensive.
  - We can fix this by normalizing the activities of neurons, such as batch normalization.
  - Batch normalization uses summed input to a neuron to compute a mean and variance to normalize summed input for each training case.
  - This paper converts batch normalization to layer norm for a single training case.
  - Layer normalization is very effective at stabilizing the hidden state dynamics in recurrent networks.
    
## Introduction
- Deep Neural Networks with SGD outperform other supervised learning tasks but need a long time to train.
- Often we modify forward pass to optimize the learning process.
- Batch normalization was introduced to reduce training time by adding a normalization layer within the network.
- The normalization standardizes each summed input using its mean and its standard deviation across the training data.

# Problems
- Summed input for RNNs vary in length, making it difficult to apply.
- Not applicable to online learning tasks / large distributed models with small mini-batches.
- Gradients with respect to the weights in one layer are highly dependent on the outputs of the neurons in the previous layer especially if these outputs change in a highly correlated way.
- ![](/images/2024-02-20/01.png)
(batch norm, which rescales based on the variance of the dataset, which would need forward pass of through the entire dataset).
- Therefore, we use the mean and standard deviation of the mini-batch dataset, and is hardly applicable to RNNs.

# Solutions
- Does not use any dependencies between training cases as it estimates the normalization statistics from the summed inputs to the neurons within a hidden layer so the normalization

## Layer Normalization
- First, calculate layer normalization statistics over all the hidden units in the same layer as follows:
![](/images/2024-02-20/02.png)
- The above equation indicates no constraints on mini-batches
- $\bar{a}^{l}_{i}$ is normalized summed inputs to the $i$th hidden unit in the $l$th layer
- $g_{i}$ is a gain parameter scaling the normalized activation before the non-linear activation function. 
  
# Layer Normalized recurrent neural networks



## Result 
  
## Conclusion
