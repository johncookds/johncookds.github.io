---
layout: post
title:  "NN Theory-Part 1: Universal Approximation (in-progress)"
date:   2020-06-05 17:57:47 -0700
categories: thoughts
---

*this is meant to be part of an ongoing series discussing theoretical aspects of neural networks*

# Universal Approximation

The ["Universal Approximation Theorem"][UniversalApprox] is generally known as something resembling "in theory neural networks can approximate any function". Sometimes the term "continuous function" is used for a more accurate description. However, given that we define our space with a large number of inputs the "continuous function" distinction is often not necessary (in my mind, the more dimensions in a space the easier it is to approximate any function with a continuous function, note the dimension of the input space also requires an increase in the size of the network model).

In their detail the above paper they lay out three broad reasons why the use of neural networks may fail:

## 1. Inadequate learning
This is an inability to traverse a loss function intelligently to find the desired combination of weights. With a convex loss function, this is not a problem but such a problem often arises in practical applications. From a given point on the loss function the probability that their is a monotonically decreasing path to the optimal solution decreases with the number of parameters involved(the probability that the optimal solution (and local minima(maybe?) are good approximations also increases though)). 
It is perhaps no wonder then that much of the dynamic currently is to gather the largest collection of computational resources to build the largest model and then try to train it. Cynically, as the larger the model is, the harder it is to train, and thus for a variations on a given task the more likely it is that an alternative training regime may produce a better outcome. To put it a different way, if you're not solving a problem well there are a lot of ways to do better. I've written further on this here: [Thoughts on Inadequate Learning][InadequateLearning].

## 2. Insufficient number of hidden units:
## 3. Lack of a deterministic relationship between input and target:

# References

[Universal Approximation Paper][UniversalApprox]

[InadequateLearning]: https://johncookds.github.io/thoughts/2020/06/07/Thoughts-on-Inadequate-Learning.html

[ConvNetPruning]: https://arxiv.org/pdf/1611.06440.pdf

[UniversalApprox]: http://cognitivemedium.com/magic_paper/assets/Hornik.pdf

[WeightAgnostic]: https://arxiv.org/pdf/1906.04358.pdf
