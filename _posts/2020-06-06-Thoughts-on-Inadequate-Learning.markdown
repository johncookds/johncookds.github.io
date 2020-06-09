---
layout: post
title:  "Thoughts on Inadequate Learning"
date:   2020-06-06 17:57:47 -0700
categories: thoughts
---

## Inadequate Learning

The problem of inadequate learning is an inability to traverse a loss function intelligently to find the desired combination of weights. With a convex loss function, this is not a problem but such a problem often arises in practical applications. From a given point on the loss function the probability that their is a monotonically decreasing path to the optimal solution decreases with the number of parameters involved(the probability that the optimal solution (and local minima(maybe?) are good approximations also increases though)). 

It is perhaps no wonder then that much of the dynamic currently is to gather the largest collection of computational resources to build the largest model and then try to train it. Cynically, as the larger the model is, the harder it is to train, and thus for a variations on a given task the more likely it is that an alternative training regime may produce a better outcome. To put it a different way, if you're not solving a problem well there are a lot of ways to do better. 

My worry is not that the problem is not being solved well in an absolute sense (crazy advances have been made, especially in NLP and CV), but that instead of shaping the loss function we are essentially pushing down the loss function (and may inadvertently be flattening it). By pushing down the loss function I mean we are getting better at approximating the optimal solution but all points on the loss curve are getting better at approximating it at once. This may not necessarily be a bad thing, as it could be that it's the structure that matters, just look at results such as [weight agnostic neural networks][WeightAgnostic], however, it seems quite inprobable that its only the structure or even primarily the structure. In addition to the weight agnostic architectures there are the collection of papers looking at [neural network pruning][ConvNetPruning], which show that, for some tasks, up to a 90% decrease in the number of weights can lead to only minimal loss in performance. While a super useful result, this is exactly what we would expect with the "pushing down the loss function" paradigm.

My issue is that I believe much of the new "learning paradigm" papers are a result of the "pushing down the loss function" which is being primarily driven by model complexity. In my mind, we are pushing our loss function onto a lower bound, which is defined somewhat by complexity but also by the relationship of our training to our testing dataset, and our architecture. If this lower bound was close to the optimal solution this would be incredible and we would find that there are many ways to train human-level capabilities for a given task. I think there is significant reason to believe that this lower bound is lower in some fields than others, lower in NLP than CV for example (even less for RL), but not even close to the optimal level. By pushing our loss function onto that lower bound we are flattening it. In the general discussion this is thought of an inability to learn, but in this case it manifests itself in an increased probability that we can make an adjustment to our training/learning regime that will lead to better results, but be false, in that it is specific to that dataset, that configuration, etc.

Shifting towards a more "shaping the loss function" approach may require two things:

1. A focus on applying a new learning technique to multiple different problems or a more general task (rather than let's say specifically on computer vision). This assumes that techniques to "shape the loss function"
2. Robustness tests on the learning technique by seeing where in the training process it performs better, and if that generalizes to multiple initializations and different model architectures/complexities.

## Thoughts on the above thoughts

I am weary that arguing for any sort of stagnation has some aspect of manipulation involved. It is clear that at least outside the halls of the premier research institutions, advances in research do not occur in a linear fashion but yet because time is linear, even if my argument is completely wrong it may not be proven wrong for a portion of time (hopefully I at least wouldn't try making the same argument again). I do think the specific examples I give above either suggest that my framework for thinking about how learning and structure should interact are wrong or that the learning portion is significantly far off. 

It is interesting to note that much of the work being done with neuromorphic networks using STDP is a much dumber form of learning. It is quite clear that aspects of the brain, such as the 100s of neurotransmitters are much more complex than the current spiking NNs or other architectures can model but still hard to believe that the actual learning paradigm in the brain is more complex than some of the ones that have been tried in the academic literature. The obvious distinction is centralized vs decentralized with respect to the ultimate cost, but to date really very little progress has been made implementing any sort of learning ability without the use of backprop even with attempted work arounds(that are possiblities for the human brain) like looped connections etc. 

As a final thought how time is dealt with is quite different as well. In the biological space there is a leveraging of the physical laws to perform tasks such as neurotransmitter decay, neuron fire-inhibition, etc. that are likely incredibly important to learning with no associated cost. This isn't a particularly ground-breaking thought but it is the main reason why ideas such as building a neuromorphic arcitecture from the ground-up seem so interesting (assuming they encompass this idea which I expect they do). I think it's important to think about the absolute massive amount of energy that would be required to simulate the human brain rather than run one, bridging that gap could possibly lead to more discoveries than going the other way.

## References

[Convolutional Network Pruning][ConvNetPruning]

[Weight-Agnostic RL Paper][WeightAgnostic]

[ConvNetPruning]: https://arxiv.org/pdf/1611.06440.pdf

[WeightAgnostic]: https://arxiv.org/pdf/1906.04358.pdf
