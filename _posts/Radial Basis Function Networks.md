---
layout: post
title: "RBFN"
date: 2024-04-15
categories: Machine Learning
---

There is a ways to handle non-linear maching learning model, for xample

k-nearest neighbors:
- Classification : $h(x) = sign (\Sigma y_i)$
- Regression : $h(x) = \frac{1}{k} \Sigma y_i$


RBF: radial basis function measures \bold{contribution of training data point concerning its distance to the test point} -> This means that any points further away means less in the model

RBF equation : z = \frac{x - x'}{r} where r is a scale parameter which regulates the weight

RBF examples:
- Gaussian Kernel $g(z) = e^{-1/2 z^2}$
- Window Kernel = $1 if z<= 1 or 0 if z > 1$
  
Notations used in this document : kernel function => $k(x = x') = g(z)$

Kernel Regression $Nadaraya-Watson$ model
Given that the weighted sum of y-values is :
$h(x) = \frac{\Sigma k(x - x_i)y_i}{\Sigma k(x - x_i)}$

Since this assumes "non-parametric" which indicates that we do \text{not assume a fixed number of parameters}, Nadaraya-Watson uses the entire training dataset to make predictions without assuming any form(linear or polynomial). 

![](/images/2024-04-16/01.png)

As illustrated above, each data point creates a "bump", (a.k.a localized peak of weight) around data point x. (Or, other points near x have a higher influence than points further away).
The above equation is equivalent to
$h(x) = \Sigma w_{i}(x)k(x - x_{i}). $ -- 1
This means that, center a bump at every $x_{i}$ with height $w_{i}(x)$, where the width is determined by r.

What confused me : What exactly is a bump and how is it formed?
Answer : This model determines the 'influence' of data point x_{i}, and the influence of each data point to predict $\hat{y}$ of new point $x$ decreases as its distance is further away.

Another problem of above : each weight $w_{i}$ depends on $x$, test point

So we can use Simplified Prediction Model (Fix heights for all test points to $w_{i}$

$h(x) = \Sigma w_{i}k(x-x_{i}) = w^{T}z with z = [k(x-x_{1}) ... k(x-x_{n})]$. -- 2
This is a "parametric version of the RBF network", where the number of parameters = number of training data
![](/images/2024-04-16/02.png)


As you can see, the equation used from 1 to 2 is transformed from d-dimensional non-linear model to n-dimensional linear model(a.k.a. feature space transform, where non-linear transformation is defined by kernel k.

We will have to use k << n weights since using n data points for n parameters causes $overfitting$. Which looks like : 
![](/images/2024-04-16/03.png)
This is $parametric$ given that each bumps, k's, are chosen independently of n.

# Training RBF-network
k -> number of bumps
r -> scale parameter(that regulates the weighting)
$w$ -> parameters from $w_{1} ... w_{k}$
$\mu$ -> k number of centers

1) Choose any $\mu_{j}$ to represent $bx_{i}$(this is a bump using test point)
2) Compute $z_{i}$ for all $bx_{i}$ in D
3) Fit a linear model $h(x) = w^{T}z$ using $D = {(z_{i}, y_{i)}^{n}_{i=1}$





