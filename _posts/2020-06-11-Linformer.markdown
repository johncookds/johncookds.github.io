---
layout: post
title:  "Linformer"
date:   2020-06-10 17:57:47 -0700
categories: papers
---

A new paper, called ["Linformer"][Linformer], out of Facebook AI introduces the possibility of reducing the complexity of the self-attention portion of a transformer model from O(n^2) to O(n). The self-attention mechanism is that each token must attend to every other token. The key insight of the paper is that the attention of any single input of a given attention token is highly correlated with the attention of other tokens. Not only that but such attention is similar even between different attention heads, not just within a given attention head.

Given the high degree of correlation, the mechanism can replaced by a two-step process where a context-mapping matrix is done at the layer-level and then is multiplied by a head-specific weight matrix. The dimension of the context-mapping matrix drives the complexity reduction, and the smaller it is the closer to O(n) the process runs. *note the true complexity is O(nk) where k is the dimension of the context-mapping matrix*

The fact that this works seems very intuitive as many words in language are filler words for proper English structure and syntax. It is part of the reason why TF-IDF has been applied as an important part in a lot of different NLP applications before the transformer models. In other tasks that transformers have been used it may or may not be as applicable, but given the fact that most neural networks aim to bottleneck information into some distilled higher representation it seems likely that was is important for one higher representation(if each head is a different "representation"/meaningful idea) is likely to be important for the rest.

Other findings like those that show the transformer models are robust to removing a large percentage of possible paths back up this finding. I guess the next question is if all these attention heads are attending the same thing, can we get some smaller models.


[Linformer]: https://arxiv.org/pdf/2006.04768v1.pdf