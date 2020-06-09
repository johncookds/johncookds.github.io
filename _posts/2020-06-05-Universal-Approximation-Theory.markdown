---
layout: post
title:  "Universal Approximation Theory"
date:   2020-06-05 17:57:47 -0700
categories: thoughts
---

# Universal Approximation Theory

The ["Universal Approximation Theorem"][UniversalApprox] is generally known as something resembling "in theory neural networks can approximate any function". Sometimes the term "continuous function" is used for a more accurate description. However, given that we define our space with a large number of inputs the "continuous function" distinction is often not necessary (in my mind, the more dimensions in a space the easier it is to approximate any function with a continuous function, note the dimension of the input space also requires an increase in the size of the network model).

In their detail the above paper they lay out three broad reasons why the use of neural networks may fail:

## 1. Inadequate learning

This is an inability to traverse a loss function intelligently to find the desired combination of weights. With a convex loss function, this is not a problem but such a problem often arises in practical applications. From a given point on the loss function the probability that their is a monotonically decreasing path to the optimal solution decreases with the number of parameters involved(the probability that the optimal solution (and local minima(maybe?) are good approximations also increases though)). 
It is perhaps no wonder then that much of the dynamic currently is to gather the largest collection of computational resources to build the largest model and then try to train it. Cynically, as the larger the model is, the harder it is to train, and thus for a variations on a given task the more likely it is that an alternative training regime may produce a better outcome. To put it a different way, if you're not solving a problem well there are a lot of ways to do better. I've written further on this here: [Thoughts on Inadequate Learning][InadequateLearning].

## 2. Insufficient number of hidden units

This can be addressed directly by using more units per layer or adding more layers. Additionally it is indirectly addressed by adding in units which are most effective at transmitting relevant information. Common techniques such as convolutions, attention, and transformers are such examples. We can think of new neural net techniques as drasting expanding of capturing X number of hidden unit layers, thus freeing up more room for additional complexity as we push against the constraint of processing capabilities. When a new technique comes along it most often does not lead to a smaller model but a more powerful one, as powerful as the processing capabilities of the research insitution can allow. The common thread that ties most advances in architecture together is increasing the degrees of freedom or flexibility of the model. An easy example of this was attention supporting and then replacing recurrent architectures, essentially giving over control of relating different temporal periods to each other to the model. A statistical concern would be the DoF expansion would lead to overfitting issues and loss of generalizability as we see in more traditional statistical models. However, this really hasn't been the case partly because of the increase in training data(which often means the training data encompasses the entire target distribution) and the fact that regularizations methods such as L1, L2 but moreso dropout and normalizations have been very effective across a wide range of architectures.

## 3. Lack of a deterministic relationship between input and target

This is the relationship governing the possible upper-bound of a model. While really the same problem its generally split between time effects(predicting further into the future is harder) and variable effects(do our variables explain our dependent variable). For the two fields on which the most progress has been made, Computer Vision and NLP, this is not much of a problem, but for industry applications and for other widely attempted applications like self-driving cars this is a large issue. One's thoughts on the possible timeframe that self-driving cars is possible is the extent to which knowledge outside of sensors and cameras is necessary to complete the task. The most likely outcome are the cases where outside knowledge is necessary are removed from the experiment pool, either by human intervention or sectioned self-driving and manual-driving roadways.

# References

[Universal Approximation Paper][UniversalApprox]

[InadequateLearning]: https://johncookds.github.io/thoughts/2020/06/07/Thoughts-on-Inadequate-Learning.html

[ConvNetPruning]: https://arxiv.org/pdf/1611.06440.pdf

[UniversalApprox]: http://cognitivemedium.com/magic_paper/assets/Hornik.pdf

[WeightAgnostic]: https://arxiv.org/pdf/1906.04358.pdf
