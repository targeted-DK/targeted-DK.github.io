---
layout: post
title: "Layer Normalization"
date: 2024-06-09
categories: papers
---

# Layer Normalization 

# Abstract
  - Deep learning is computationally expensive.
  - We can fix this by normalizing the activities of neurons, such as batch normalization.
  - Batch normalization uses summed input to a neuron to compute a mean and variance to normalize summed input for each training case.
  - This paper converts batch normalization to layer norm for a single training case.
  - Layer normalization is very effective at stabilizing the hidden state dynamics in recurrent networks.
    
# Introduction
- Deep Neural Networks with SGD outperform other supervised learning tasks but need a long time to train.
- Often we modify forward pass to optimize the learning process.
- Batch normalization was introduced to reduce training time by adding a normalization layer within the network.
- The normalization standardizes each summed input using its mean and its standard deviation across the training data.

# Problems
- Summed input for RNNs vary in length, making it difficult to apply.
- Not applicable to online learning tasks / large distributed models with small mini-batches.
- Gradients with respect to the weights in one layer are highly dependent on the outputs of the neurons in the previous layer especially if these outputs change in a highly correlated way.
![](/images/2024-06-09/1.png)
(batch norm, which rescales based on the variance of the dataset, which would need forward pass of through the entire dataset).
- Therefore, we use the mean and standard deviation of the mini-batch dataset, and is hardly applicable to RNNs.

# Solutions
- Does not use any dependencies between training cases as it estimates the normalization statistics from the summed inputs to the neurons within a hidden layer so the normalization

## Layer Normalization
- First, calculate layer normalization statistics over all the hidden units in the same layer as follows:
![](/images/2024-06-09/2.png)
- The above equation indicates no constraints on mini-batches
- $\bar{a}^{l}_{i}$ is normalized summed inputs to the $i$th hidden unit in the $l$th layer
- $g_{i}$ is a gain parameter scaling the normalized activation before the non-linear activation function. 

- Batch normalization has an issue where the previous layer's output greatly affects the input of next layer (high correlated changes, often causing covariate shift).
- Therefore, we fix the mean and variance of statistics as follows: 
![](/images/2024-06-09/3.png) (equation 3)

## Difference between layer and batch normalization
- All the hidden units in a layer share the same normalization terms μ and σ, but different training cases have different normalization terms
- Layer normalization does not impose any constraint on the size of a mini-batch and it can be used in the pure online regime with batch size 1.

## Layer Normalized recurrent neural networks
![](/images/2024-06-09/4.png)

# Analysis

## Invariance under weights and data transformations
- Normalizing the summed inputs $a_{i}$ to a neuron in respect to the mean and variance and learning gain $g$ and bias $b$
![](/images/2024-06-09/5.png)

## Weight re-scaling and re-centering:
- The batch and weight normalization are invariant to the re-scaling of the weights because weights and mean / variance are also scaled by some $\delta$
![](/images/2024-06-09/6.png)
- Layer normalization is *not invariant* to the individual scaling of the single-weight vectors.
- Layer normalization *invariant* to scaling of the entire weight matrix and invariant to a shift to all of the incoming weights in the weight matrix as shown above.

## Data re-scaling and re-centering:
![](/images/2024-06-09/7.png)
- Layer normalization is invariant to re-scaling of individual training cases, because the normalization scalars μ and σ in Eq. (3) only depend on the current input data.

## Geometry of parameter space during learning
- normalization scalar σ can implicitly reduce learning rate and makes learning more stable.

## Riemannian metric
- Uses KL divergence metric which calculates the difference between two point's probability distributions
- KL divergence metric's parameter space is Riemannian matric, and $\delta$ below is a small change to parameters:
![](/images/2024-06-09/8.png)

## The geometry of normalized generalized linear models
- Log likelihood of generalized linear models(GLM) :
![](/images/2024-06-09/9.png)
- f(·) is the transfer function that is the analog of the non-linearity in neural networks
- f′(·) is the derivative of the transfer function
- η(·) is a real valued function
- c(·) is the log partition function
- φ is a constant that scales the output variance.
- The Kronecker product is an operation on two matrices that results in a block matrix:
If 𝐴 A is an 𝑚 × 𝑛 matrix and 𝐵 B is a 𝑝 × 𝑞 matrix, the Kronecker product 𝐴 ⊗ 𝐵 is an 𝑚𝑝 × 𝑛𝑞 matrix. Each element $a_{ij}$​ of matrix 𝐴 is multiplied by the entire matrix 𝐵.

- Applying Kronecker product of the data features and the output covariance matrix:
![](/images/2024-06-09/10.png)
![](/images/2024-06-09/11.png)

## Implicit learning rate reduction through the growth of the weight vector
- If the norm of the weight vector $w_{i}$ grows twice as large, even though the model’s output remains the same, the Fisher information matrix will be different. The curvature along the $w_{i}$ direction will change by a factor of 1/2 because the $σ_{i}$ will also be twice as large. As a result, for the same parameter update in the normalized model, the norm of the weight vector effectively controls the learning rate for the weight vector.

## Learning the magnitude of incoming weights
- Learning the magnitude of incoming weights in the normalized model is therefore, more robust to the scaling of the input and its parameters than in the standard model. 

# Result [skip]
  
# Conclusion
- Layer normalization is invariant to per training-case feature shifting and scaling.
- Shows that recurrent neural networks benefit the most from the proposed method(layer norm) especially for long sequences and small mini-batches.

