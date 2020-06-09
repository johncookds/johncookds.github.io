---
layout: post
title:  "GPT vs BERT Thoughts"
date:   2020-06-09 17:57:47 -0700
categories: thoughts
---


The key similarity between all current SOTA language models is there use of transformers, first introduced in [Attention is All You Need][AttentionIsAllYouNeed]. While there have been changes made to either the architecture, the training regime, or training regime as new and alternative models based off these two are released the main distinction is the uni-directional GPT architecture(the uni-directional component is done using a masked attention layer) vs the bi-directional BERT architecture(built after, and in response to uni-directional language models). This effects the input and the types of tasks each performs best on. The actual architecture differences are not covered here in detail but they are covered in [this blog post][EncoderDecoderExample].

In the simplest terms the BERT model, runs once, predicts a chunk of values based on a chunk of input text, whereas the GPT model will predict the next word based on the previous words. Thus, for a text generation task BERT may be underequipped needing to process and output in text chunks vs a more natural iterative approach from the GPT framework. In the broad terms thinking about BERT as a model built to understand NLP(focused primarily on semantic understanding) and GPT as a model built to generate NLP(focused primarily on structure) is helpful. Leaving aside the larger training data and architecture, what has been suprising so far is the ability of GPT to both learn language structure and encode semantic understanding.

GPT is designed for cases of text generation, and is built not as a specific NLP task solver, but a general purpose model. What I mean by "general prupose" is any task you want GPT to perform must be embedded into the input text itself. One could think of GPT then being given an extra task, even before attempting to produce the right answer it must determine what task it is given. Theoretically, this could be one way we finally decide that a future GPT model(GPTv3 is not there yet) is actually reasoning. The architecture and input space is possible to present GPT with unseen tasks that if it could reason could answer is there, the question of how GPTv3 performs in rigorous testing would be a great question to answer, but its size prohibits all but its creators and a few choice others from really discovering where GPTv3 fails (some examples are given in the papers, however, obviously the incentive is to focus more on what the new model can do rather than cannot). Regardless of this though, it is really the ability of GPTv3 to act on contextualized input so well, meaningfully changing next word probabilities based on contextual information. To draw a comparison to multi-modal tasks of image generation, in those cases the images output are often the mean of the modes, looking like some poorly done mashup of the two classes of images. GPT doesn't seem to have this problem and is able to fit specific modes in the distribution while maintaining solid structure. Perhaps it is the fact of a common structure across modes that allows for this to occur (additionally for NLP tasks we are starting much further into understanding than the image space, words or parts of words seem much closer to semantic understanding than pixels are to image understanding).

BERT is best described as a multi-purpose NLP understand-er(?). Built using a masking and a next-sentence prediction framework, its weights probably encode less of the structure of the english language as opposed to word-relation understandings (note obviously these are just my opinions). This understanding is probably the most necessary sub-task for most NLP solutions. 

Any task that can be encoded as text can be applied to GPT, however, any tasks beyond language generation are most likely best done using BERT or a BERT-derivative primarily because of its task-oriented versions and the possibility of fine-tuning the model(which is probably not feasible for most situations given the GPTv2/GPTv3 setup and size). As of right now it seems the best concievable GPT model could most likely outperform the best concievable BERT model if only due to its enormous size, but also the way its input tasks are structure the task description alongside additional information do more closely mimic human language understanding. It seems reasonable this could lead to more of a sharing of weights across tasks and forcing them to represent some type of actual language understanding vs the encoding of seperate tasks on the same network(which would be the bear case for BERT).


## References

[AttentionIsAllYouNeed][AttentionIsAllYouNeed]

[Good blog post on GPT-2 BERT Architectures][EncoderDecoderExample]

[AttentionIsAllYouNeed]: https://arxiv.org/pdf/1706.03762.pdf

[EncoderDecoderExample]: https://jalammar.github.io/illustrated-gpt2/