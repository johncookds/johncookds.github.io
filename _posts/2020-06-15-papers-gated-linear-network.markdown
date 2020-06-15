---
layout: post
title:  "Paper - Gated Linear Networks"
date:   2020-06-14 17:57:47 -0700
categories: papers
---

Deepmind recently released a [paper introducing "a new family of backpropagation-free neural networks" called Gated Linear Networks][paper]. The most important thing to note about GLN's is each neuron is actively trying to predict the target which means its weights can be updated locally without the need for backpropagation through higher layers. A GLN's non-linearity comes from the selection of which weights to apply, importantly only the chosen weights are trained not the actual context function that selects which weights are chosen. which makes any single prediction the product of a series of matrix multiplication a.k.a. a linear set of features allowing for what the paper describes as the "construction of meaningful saliency maps".

![GLN Framework](https://johncookds.github.io/assets/3/glns.png)

## Learning Algorithm

The above image from their [paper][paper] shows the broad outline of their network. Each neuron boils down to combining the previous layer's probabilities(through geometric mixing) based on a selected vector of weights. The data is essentially choosing its linear path through the network based on the gated function. Additionally remember that the gated function is fixed and thus needs to carve up the data into meaningful chunks based on its initialization. Note that this matrix is implemented and described for binary classification. I don't see any glaring reason why it couldn't be expanded to fit the multi-classification case however(whether or not it would perform well is a separate issue).

![GLN Algorithm](https://johncookds.github.io/assets/3/gln_algorithm.png)

### Aggregating the previous layer's predictions

The first computation performed is the application of the logit function to each incoming probability, which we can think of this as undoing the sigmoid function previously applied to attain the probability in the last layer. The weights chosen by the context function(discussed below) are then applied to these log-odds. The paper presents the aggregation as a type of mixing based on the "product of experts" as opposed to some linear combination. By using the product each incoming probability has the "right to veto", in that if it is very close to 0 with a non-zero associated weight it can drive the geometric mixture close to zero. In broader terms I think it makes sense to think of this product mixing as being more expressive than alternative mixing forms(mainly linear combination) and can then allow the weight vectors to vary more widely in terms of adapting a single neuron's output to the specific vector chosen by the context function. A sigmoid is then applied to the product mixing output to attain a probability.  *Note a bias term is included as well but I believe this is not trained and is referenced in the paper of being set at .5, it is possibility included for stability reasons but does not seem very important overall to me*

![Geometric Mixing](https://johncookds.github.io/assets/3/geometric_mixing.png)

### Designing the context function

The context function or gate is the function which introduces the complexity into the model. They carve up the original data(what they term the "side information") by using half-space functions. Half-space functions essentially divide some space into two parts(the function essentially defines a hyperplane which divides an affine space). They use these half-space functions to carve the space with d dimensions, where d is the dimensionality of the original data, into one of their i matrices.

![half-space function](https://johncookds.github.io/assets/3/halfspace.png)

The author's initialization problem is essentially to find v vectors such that the d-dimensional space is appropriately carved by those functions. They can do this by uniformly sampling points from a d-dimensional unit sphere. I am curious as to how dependent this context function approach is to the distribution of the original data, in lower dimensions I think its easy to concoct an example where much of the data could fall into a single context bucket, however with data of higher dimensions, the data may inherently be spread out enough for this approach to work. Additionally, I do not believe they show any experiment where they use binary values(discrete variables) in the input.

## Results

The model reports many results which compare it against a generic MLP and SVM model showing comparable performance in tasks. These are necessary to have so that they show their method at least somewhat works but other than that not very interesting. The two interesting parts are the creation of saliency maps and the multi-task learning.

### Saliency Maps

The ability to visualize what is occurring inside neural networks has gotten much better, whether it is looking at hidden layers inside a convolutional network or at feature explainers such as SHAP or LIME. Many industry applications are not visual and thus need to rely on feature explainers to attempt to explain why the model is doing what it is doing. Often times these are sold as(or their just isn't the required knowledge on the other side of the table) being as reliable as other feature explainers one would show from traditional statistics like variable coefficients, this is not great and even though the explainers are quite useful in many situations where input variables are highly correlated they given pretty strong disclaimers as the accuracy of their results(note this is obviously the case with a linear model as well, although a linear model is often less likely to have highly correlated values if modeled correctly). In contrast to the "estimated" explainers the GLN model can allow for the creation of saliency maps as any given prediction can be represented by a multi-linear polynomial of degree L where L is the number of layers. I'm unsure how in a straight forward prediction based on tabular data one would present such a map in an intuitive way but it is a useful indicator that their network is learning a useful pattern in the MNIST case below, where the maps seem to trace the desired class in red against the blue background representing the possible solution space. 

![saliency map](https://johncookds.github.io/assets/3/saliency_map.png)

### Catastrophic Forgetting

The authors train the model on a sequential MNIST task(where all of 1-class is presented then all of another) and show their GLN performs very well in this case. This is interesting but kind of an expected result from how they design the network, my critique would be that they essentially prevent this catastrophic forgetting by pre-dividing the possible input space. This pre-dividing is mainly based on the number of weight vectors they allow for a given neuron or to put it another way the number of context vectors they define. The upshot is that the complexity of the model seems to scale linearly with the complexity of the task, whereas this is hopefully not the case for traditional neural networks(even with the drastic increase in complexity and computation I think for NN's its sublinear). My argument is that as you add digits to recognize you will need to add weight matrices to this network more frequently than you'd need to add complexity to other networks and that once you get to a suitably complex task this problem will swamp all other concerns and you'd need an incredibly massive network(this is obviously a completely new network design so maybe this is unfair).

## General Thoughts

I think of this architecture as a smart ensemble network which relies heavily on the ability of the context vector to appropriately assign data points to weight vectors. In all the tasks they show I think there is the possibility of a linear classification on the pixel space performing better than random and i would worry about tasks where this is not possible. When this is not possible i worry the inability to generate higher layer features will lead to garbage being passed through the network, vs now where their are iterative improvements in the probability. The flip-side to this is maybe all problems can be condensed into binary sub-problems that aren't too complex but then you could have an incredible number of models.

There is a certain Bayesian idea in this network which I like with both the first layer needing to be defined, essentially operating as a prior, and each neuron of the next layer incorporating probability estimates based on it's own information(which is represented by the gated context function).

[paper]: https://arxiv.org/pdf/1910.01526.pdf
