---
layout: post
title:  "GPT-3 Paper Released: GPT vs BERT Thoughts"
date:   2020-06-09 17:57:47 -0700
categories: thoughts
---


The key similarity between all current SOTA language models is their use of transformers, first introduced in [Attention is All You Need][AttentionIsAllYouNeed]. While there have been changes made to either the architecture, the training regime, or training regime as new and alternative models based off these two are released the main distinction is the uni-directional GPT architecture(the uni-directional component is done using a masked attention layer) vs the bi-directional BERT architecture(built after, and in response to uni-directional language models). This effects the input and the types of tasks each performs best on. The actual architecture differences are not covered here in detail but they are covered in [this blog post][EncoderDecoderExample].

In the simplest terms the BERT model, runs once, predicts a chunk of values based on a chunk of input text, whereas the GPT model will predict the next word based on the previous words. Thus, for a text generation task BERT may be underequipped needing to process and output in text chunks vs a more natural iterative approach from the GPT framework. In the broad terms thinking about BERT as a model built to understand NLP(focused primarily on semantic understanding) and GPT as a model built to generate NLP(focused primarily on structure) is helpful. Leaving aside the larger training data and architecture, what has been suprising so far is the ability of GPT to both learn language structure and encode semantic understanding. Note the new [GPT-3 paper][GPT3] the GPT-3 model outperforms BERT on a lot of tasks, which is understandable given the massive size difference, it does not on reading comprehension tasks however. This may suggest GPT-3 having an incredible prior distribution of word and symbol relationships rather than some advanced concept of how to process and understand text.

GPT is designed for cases of text generation, and is built not as a specific NLP task solver, but a general purpose model. What I mean by "general prupose" is any task you want GPT to perform must be embedded into the input text itself. One could think of GPT then being given an extra task, even before attempting to produce the right answer it must determine what task it is given. This is done in their [new paper][GPT3] where they show "few shot learning" by using an input a prompt and few example answers, then have the model complete an un-complete example. For example: the input may be
"translate English to French: Cheese: Fromage, Pineapple: " and the model would have to output "ananas".
Theoretically, this could be one way we finally decide that a future GPT model(GPT-3 is probably not there yet) is actually reasoning. The architecture and input space is possible to present GPT with unseen tasks that if it could reason could answer is there, the question of how GPT-3 performs in rigorous testing would be a great question to answer, but its size prohibits all but its creators and a few choice others from really discovering where GPT-3 fails (some examples are given in the papers, however, obviously the incentive is to focus more on what the new model can do rather than cannot). In the paper they do show the paper performing poorly at reading comprehension and math tests. However, where it goes wrong I think can be argued as being caused by a lack of training data as is done in this [youtube review of the paper][GPT3Video]. Regardless of this though, it is really the ability of GPT-3 to act on contextualized input so well, meaningfully changing next word probabilities based on contextual information. If all the model is doing is some sort of memorizing the input it is doing incredibly well.

To draw a comparison to multi-modal tasks of image generation, in those cases the images output are often the mean of the modes, looking like some poorly done mashup of the two classes of images. GPT doesn't seem to have this problem and is able to fit specific modes in the distribution while maintaining solid structure. Perhaps it is the fact of a common structure across modes that allows for this to occur (additionally for NLP tasks we are starting much further into understanding than the image space, words or parts of words seem much closer to semantic understanding than pixels are to image understanding).

BERT is best described as a multi-purpose NLP understand-er(?). Built using a masking and a next-sentence prediction framework (vs GPT which is solely a next-word prediction framework), its weights probably encode less of the structure of the english language as opposed to word-relation understandings (note obviously these are just my opinions). This understanding is probably the most necessary sub-task for most NLP solutions. 

Any task that can be encoded as text can be applied to GPT, however, any tasks beyond language generation are most likely best done, considering performance but moreso computation cost, using BERT or a BERT-derivative primarily because of its task-oriented versions and the possibility of fine-tuning the model(which is probably not feasible for most situations given the GPT-2/GPT-3 setup and size). *Note: BERT is designed in a pre-training, fine-tuning framework wheras GPT is not. Obviously fine-tuning is always possible but it is clearly not what GPT is for, it is more designed, in the task sense, to be a master computer that knows all answers and can perform better with a little prompting*

The release of GPT-3 showed some very impressive results but the results probably do not represent a meaningful step on a much better language model and just a massively scaled up version of the transformer network we saw in GPT-2. Providing these massive models(even just kinda-massive like BERT) with some ability to query for relevant information may allow it to free up weights for more higher order thinking, leading to greater demonstration of what could come close to reasoning. This would be something akin to many of the models being discussed in the first wave of AI in the 90's but on a completely different scale with the model having much better control over possible "querying space" and control over the english language/syntax as well.

## References

[AttentionIsAllYouNeed][AttentionIsAllYouNeed]

[GPT-3 Paper][GPT3]

[Yannic Kilcher's GPT-3 Review Video][GPT3Video]

[Good blog post on GPT-2 BERT Architectures][EncoderDecoderExample]

[AttentionIsAllYouNeed]: https://arxiv.org/pdf/1706.03762.pdf

[EncoderDecoderExample]: https://jalammar.github.io/illustrated-gpt2/

[GPT3]: https://arxiv.org/pdf/2005.14165.pdf

[GPT3Video]: https://www.youtube.com/watch?v=SY5PvZrJhLE