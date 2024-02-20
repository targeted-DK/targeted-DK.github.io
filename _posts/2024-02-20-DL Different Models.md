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


**Input / Output channel calculation** 
![](/images/2024-02-20/02.png)


2. ZFNet: ILSVRC 2013 winner 
![](/images/2024-02-20/03.png)

3. VGGNet
![](/images/2024-02-20/04.png)
![](/images/2024-02-20/05.png)
![](/images/2024-02-20/06.png)
![](/images/2024-02-20/07.png)


The key characteristics of VGGNet are its simplicity and depth. It uses a series of convolutional layers with small 3×3 receptive fields followed by max pooling layers. One of the main innovations of VGGNet was to demonstrate that the depth of the network is a critical component for achieving good performance, which they achieved by using many stacked convolutional layers.
All convolutional layers use the same kernel size (3×3) and stride (1), with padding to preserve spatial dimensions. Max pooling is used to reduce the feature map by half after each block. The use of 3×3 convolutional filters in succession (stacking them) has a similar receptive field to larger filters but with fewer parameters and deeper representations. VGGNet is very homogeneous, only using 3×3 convolutions and 2×2 pooling from the start to the end of the network.


5. GoogLeNet
![](/images/2024-02-20/08.png)


6. ResNet
![](/images/2024-02-20/09.png)
![](/images/2024-02-20/10.png)

The key innovation of ResNet is its use of residual blocks, where the input to a block is added to the output of that block. This design addresses the vanishing gradient problem and enables the training of much deeper networks than was previously feasible. The vanishing gradient problem, where gradients become increasingly smaller through each layer, making it difficult to update the weights in the initial layers, had been a significant barrier to the effectiveness of deep neural networks.

A residual block typically consists of a few convolutional layers followed by batch normalization and ReLU activations. The input to the block is added to the output of the block (after these layers) before applying the final activation function. This can be represented as:

**Output $\=Activation(F(x)+x)\\text{Output} = \\text{Activation}(F(x) + x)Output\=Activation(F(x)+x)$ **

*   F(x)F(x)F(x) represents the operations performed by the convolutional layers within the block.
*   xxx is the input to the block.
*   The operation F(x)+xF(x) + xF(x)+x is what constitutes the "residual connection," essentially allowing the block to learn a residual mapping.

8. DenseNet
![](/images/2024-02-20/11.png)

