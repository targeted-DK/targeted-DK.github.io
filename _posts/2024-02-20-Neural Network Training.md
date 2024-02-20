---
layout: post
title: "Neural Network Training"
date: 2024-02-20
categories: ComputerVision
---

## Neural Network Training

1. Optimizations
- Mini-batch SGD
- Learning rate decay
- Diagnosing learning curves
- Adaptive optimization methods such as SGD with momentum, RMSProp, Adam

Optimization in NN is an uncertain process - and may not be theoretically perfect. Improvements in training strategy is architectures makes it hard to see where contributions exactly come from. 

![](/images/2024-02-20.2/01.png)
Mini-batch SGD is to take a batch of size b and computer the gradient, average them, and update the weights. Hyperparameters are batch size, learning rate decay, and when to stop the iteration.


![](/images/2024-02-20.2/02.png)
*Size of batch*
Try to run with the large mini-batch size and use up much gpu as possible in the first place. Smaller mini-batches have more noise but have less overhead, and are also obviously quicker. 
Most state-of-the-art models are trained on large mini-batches(more than thousands), so you need large simultaneous computer clusters to run the algorithm.

![](/images/2024-02-20.2/03.png)

We need a large number of samples for backward passes and gradients. There is a hard limit on the size of mini-batches if you have limited GPUs. So you can use gradient accumulation allows you to solve this problem. (multiple forward passes to accumulate gradients). Mini-batch size is something that flows all the way down the stream. 

*Importance of learning rate*
![](/images/2024-02-20.2/04.png)

Smaller batches give large noise, so using larger batches helps you avoid local minima by avoiding noises. 
Small Batch Sizes: Training with smaller batch sizes introduces more noise into the gradient estimation, which can help escape local minima but might also lead to a more erratic training process. To compensate for the increased noise, a smaller learning rate is often preferred to ensure stable convergence. However, some research suggests that with careful tuning, higher learning rates can be beneficial even for small batches, potentially leading to faster convergence and better generalization. 

Large Batch Sizes: Larger batches provide a more accurate estimate of the gradient but require more computational resources per update. With larger batch sizes, it's often possible to use a higher learning rate since the gradient estimate is more stable. However, too high a learning rate can still cause instability. There's also a phenomenon observed where excessively large batch sizes may lead to poorer generalization performance.

*Different learning rate schedules*
![](/images/2024-02-20.2/05.png)

![](/images/2024-02-20.2/06.png)

*SGD variations*

- SGD with momentum (Mostly used SGD in practice)
![](/images/2024-02-20.2/07.png)
![](/images/2024-02-20.2/08.png)

Momentum is a modification to SGD that accelerates the convergence towards the minimum of the loss function by incorporating the weighted average of past gradients into the current update. This is akin to adding inertia to the optimization process, helping to overcome local minima and reducing oscillation.

- Adagrad : Adaptive per-parameter learning rates
![](/images/2024-02-20.2/09.png)

We are keeping track of kinds of update we are making. If we make big updates, we will make small updates in the future(due to denominator in the equation)
Adagrad adapts the learning rate on a per-parameter basis, allowing for more frequent updates of more sparse parameters and less frequent updates for less sparse ones. This is particularly useful for dealing with sparse data (e.g., in natural language processing tasks).


- RMSProp
![](/images/2024-02-20.2/10.png)

Instead of accumulating like Adagrad, it decays the previous update. Instead of accumulating all past squared gradients, RMSProp uses a moving average, making it more suitable for training deep neural networks. 
​

- Adam
![](/images/2024-02-20.2/11.png)
 
Adam (Adaptive Moment Estimation) combines ideas from both Momentum and RMSProp. It maintains two moving averages for each weight: one for the gradients (like Momentum) and one for the squared gradients (like RMSProp). This is the best default to use in practice - because of weight decaying in the recent history of updates, it is less sensitive to having a correct learning rate. So it is easier to pick a learning rate.



2. Massaging the numbers
- Data augmentation
- Data preprocessing
- Weight initialization
- Batch normalization


*Data augmentation*
![](/images/2024-02-20.2/12.png)
It increases variability and adds data points so that in provides more generalized model. Think it as adding a *specialized noise* to minic different variations of image and preserve the semantics of original image.
This is a technique used in machine learning and deep learning to increase the diversity of a training dataset without actually collecting new data. This is achieved by applying a series of transformations that alter the training samples but do not change their labels. Data augmentation is particularly valuable in scenarios where acquiring additional labeled data is costly or impractical. It helps to prevent overfitting and improve the model's generalization capabilities by providing a broader set of variations to learn from.
 

*Data Preprocessing*
![](/images/2024-02-20.2/13.png)
![](/images/2024-02-20.2/14.png)
![](/images/2024-02-20.2/15.png)
![](/images/2024-02-20.2/16.png)

*Weight Initialization*
![](/images/2024-02-20.2/17.png)


*Batch Normalization(배치 정규화)*
![](/images/2024-02-20.2/18.png)
![](/images/2024-02-20.2/19.png)
![](/images/2024-02-20.2/20.png)
![](/images/2024-02-20.2/21.png)




3. Regularization
4. Test Time - averaging predictions and ensembles

