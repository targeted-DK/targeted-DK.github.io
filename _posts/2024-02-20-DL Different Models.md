---
layout: post
title: "Convolutional Architecture 2"
date: 2024-02-20
categories: ComputerVision
---

## Convolutional Architecture 2

1. AlexNet

![](/images/2024-02-20/01.png)
**Max Pooling**: AlexNet introduced the use of max pooling to reduce the spatial dimensions of the input volume for the subsequent layers. 
Max pooling helps in reducing the computational load and also helps in making the detection of features invariant to scale and orientation changes.

**ReLU Nonlinearity**: AlexNet was one of the first convolutional neural networks (CNNs) to use the Rectified Linear Unit (ReLU) as the activation function, which was a departure from the sigmoid functions commonly used at the time. ReLUs help in solving the vanishing gradient problem, allowing models to learn faster and perform better.

*Vanishing gradient problem* : It refers to the condition where the gradient of the loss function decreases exponentially as it propagates back through the network's layers during training, using the backpropagation algorithm. This problem primarily affects networks with many layers and traditional activation functions, such as the sigmoid or hyperbolic tangent (tanh), which are prone to saturating.
When the gradients become very small, the weights of the early layers in the network barely get updated. This leads to very slow learning and, in practical terms, means that the network cannot learn complex patterns in the data, especially those patterns that depend on the interactions of inputs described by the early layers.

*Saturation* : 
Saturation, in the context of artificial neural networks and their activation functions, refers to a state where the activation function's output does not significantly change for a wide range of input values. This behavior is most commonly associated with sigmoid and hyperbolic tangent (tanh) activation functions, which asymptotically approach their maximum and minimum output values.

*Solutions to vanishing gradient problem* : 
- ReLU (Rectified Linear Unit) Activation Function: 
Use of ReLU (Rectified Linear Unit) Activation Function: Unlike sigmoid and tanh, the ReLU function does not saturate in the positive domain, which helps in preventing the gradients from vanishing.
- Weight Initialization Techniques: Proper initialization of weights, such as He or Xavier initialization, can help in maintaining a stable gradient flow across layers.
- Residual Networks (ResNets): These networks introduce skip connections that allow gradients to flow through the network more easily, bypassing some layers entirely.
- Batch Normalization: This technique normalizes the inputs of each layer to have a mean of zero and a variance of one. This can help in preventing gradients from getting too small or too large.
