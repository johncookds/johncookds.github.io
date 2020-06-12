---
layout: post
title:  "Random Idea Repo"
date:   2020-05-26 17:57:47 -0700
categories: thoughts
---

*These are random ideas which I've most likely stolen from somewhere but cannot remember where.*


## NLP

Including marked tokens in the training of an NLP model to allow for, once inference is being done, specific output tokens to suggest which portions of the training dataset the model is drawing its influence from. - ML Street Talk GPTv3

Adding a loss term to maximize entropy between the attention heads of transformer model - I expect someone has done this? Could also train individual heads and stack while training. All to get out of convergence of different heads idea. Thought behind this is the Linformer paper points to something bad that needs to be solved.
[Multi-Head Attention with Disagreement Regularization][https://arxiv.org/pdf/1810.10183.pdf]

## CV

Using a style GAN as image manipulation for pre-training a self-supervised tasks. All self-supervised tasks require an intial augmentation of images and essentially train a model to relate those manipulations. Those manipulations occur mainly in the pixel space vs some higher conceptual space. We can see this by models failing when asked to identify an object of a super different style. A style GAN (and maybe other types of GANs) could provide an interesting dimension on which to vary an image, relating more to how humans higher-levle object detection and object relating actually occur than more pixel-based techniques.
