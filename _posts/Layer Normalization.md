---
layout: post
title: "[Paper] Layer Normaliztion"
date: 2024-06-09
categories: papers
---

## Abstract
  - Deep learning is computationally expensive
  - We can fix this by normalizing the activities of neurons, such as batch normalization
  - Batch normalization uses summed input to a neuron to compute an mean and variance to normalize summed input for each training case
  - This paper converts batch normalization to layer norm for a single training case.
  - Layer normalization is very effective at stabilizing the hidden state dynamics in recurrent networks.
    
## Introduction
- Deep Neural Networks with SGD outperform other supervised learning tasks but needs a long time to train.
- Often we modify forward pass to optimize the learning process.
- Batch normalization was introduced to reduce training time by adding a normalization layer within the network.
- The normalization standardizes each summed input using its mean and its standard deviation across the training data.

# Problems
- Summed input for RNNs vary in length, making it difficult to apply.
- Not applicable to online learning tasks / large distributed models with small mini-batches.

# Solutions
- Does not use any dependencies between training cases as it estimates the normalization statistics from the summed inputs to the neurons within a hidden layer so the normalization

## Related work
# Standard RNN


## Result 
  
## Conclusion
