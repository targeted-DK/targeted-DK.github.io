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

**What is batch?**

Data points are chosen for a batch through a process typically referred to as "batching," which can be implemented in various ways depending on the specific requirements of the training process and the desired outcomes. Here are some of the common strategies for selecting data points for a batch:

1. Random Batching
The most common method is **random batching**, where data points are randomly selected from the dataset to form a batch. This approach ensures that each batch is a representative sample of the dataset, which helps in generalizing the learning across the dataset. Random batching is implemented by **shuffling the entire dataset** at the beginning of each epoch and then partitioning it into batches of the specified size.

2. Sequential Batching
In some cases, especially when **dealing with time series data** or data where sequential order matters (e.g., text data for natural language processing tasks), data points are selected sequentially to maintain the temporal or logical sequence within each batch. This method respects the order of data, which is crucial for models like RNNs (Recurrent Neural Networks) that rely on the sequence of data points.

3. Stratified Batching
Stratified batching involves selecting data points in such a way that each batch **mirrors the distribution of classes** or key characteristics of the entire dataset. This approach is particularly useful in classification tasks where the dataset might be imbalanced, i.e., some classes are underrepresented compared to others. Stratified batching ensures that each batch contains a proportional number of samples from each class, helping the model learn more evenly across different classes.

4. Balanced Batching
Similar to stratified batching but more focused on balancing specific attributes, balanced batching ensures that batches are constructed to balance specific criteria, such as class distribution, but can also extend to other attributes like difficulty level, size of the input data, or other domain-specific characteristics. This method can be more complex to implement, as it requires a prior understanding or analysis of the dataset to define the balancing criteria.
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

*Other types of normalizations*

![](/images/2024-02-20.2/22.png)
- Layer Normalization (Ba et al., 2016) normalizes the inputs across the features for each training example. Unlike batch normalization, which normalizes across the batch dimension, layer normalization computes the normalization statistics (mean and variance) for each individual example and across all features. This makes it particularly effective for recurrent neural networks (RNNs) and situations where batch sizes are small. Key benefits: It reduces the impact of the internal covariate shift, leading to faster training and higher overall stability of the neural network. It also works well in situations where the batch size is variable or small.
- Instance Normalization (Ulyanov et al., 2017) is similar to layer normalization but is used primarily in convolutional neural networks (CNNs), especially in tasks like style transfer. It normalizes the feature maps independently for each instance (image) in the batch, computing the mean and variance for each feature map separately. Key benefits: It helps models to generalize across changes in contrast, which is particularly useful in style transfer applications, where the contrast of the content image should not affect the style transfer.
- Group Normalization (Wu and He, 2018)divides the channels (features) of each layer into groups and normalizes the features within each group. This is useful because it doesn't depend on the batch size, making it suitable for tasks where batch sizes are small or vary significantly. Key benefits: It provides a middle ground between instance normalization and layer normalization, offering benefits in a wide range of tasks, particularly when batch sizes are small or when using highly variable data.
- Weight Normalization (Salimans et al., 2016) is a technique that decouples the magnitude of the weights from their direction, which helps to speed up convergence by improving the conditioning of the optimization problem. It normalizes the weights of each neuron to have unit norm, which is a different approach from the other normalization techniques that focus on the activations. Key benefits: It can lead to faster convergence by reducing the complexity of the optimization landscape. It's particularly useful in optimizing deep networks and can be combined with other normalization methods for improved performance.

3. Regularization
![](/images/2024-02-20.2/23.png)
![](/images/2024-02-20.2/24.png)
![](/images/2024-02-20.2/25.png)

It is important to note that a higher hyperparameter makes the model simpler by **adding** penalties. In other words, The idea is to add an extra term to the loss function, which increases the total loss based on the magnitude of the model parameters (weights). This encourages the model to keep the weights small, which in turn simplifies the model. 

*   **L1 Regularization (Lasso):** Adds a penalty equal to the absolute value of the magnitude of coefficients. This can lead to coefficients being reduced to zero, effectively selecting a simpler model by excluding some features.
    
   $ L\=Loriginal+λ∑i\=1n∣wi∣L = L\_{original} + \\lambda \\sum\_{i=1}^{n} |w\_i|L\=Loriginal​+λ∑i\=1n​∣wi​∣ $
    
*   **L2 Regularization (Ridge):** Adds a penalty equal to the square of the magnitude of coefficients. This discourages large weights but does not set them to zero, leading to a model where the contribution of each feature is minimized but not entirely excluded.
    
  $  L\=Loriginal+λ∑i\=1nwi2L = L\_{original} + \\lambda \\sum\_{i=1}^{n} w\_i^2L\=Loriginal​+λ∑i\=1n​wi2​ $


5. Test Time - averaging predictions and ensembles

