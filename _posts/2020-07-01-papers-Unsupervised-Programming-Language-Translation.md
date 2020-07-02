---
layout: post
title:  "Paper - Unsupervised Programming Language Translation"
date:   2020-07-01 17:57:47 -0700
categories: papers nlp
---

The paper [Unsupervised Programming Language Translation][paper] is a recent paper which shows impressive results in applying a transformer language model to completely unsupervised language translation. The fact that the model is still able to perform when not being trained on a supervised language task is quite a feat. The model is able to exploit both common structure and shared syntax between languages to essentially bootstrap translation from initial masking and denoising tasks. The model is tested on translation tasks between Java, Python and C++ and performs best for translation between the two most structurally similar languages, Java and C++. Between structurally different languages a greater level of abstraction would clearly need to be learned. 

One avenue of further research that might be interesting would be to find two languages that are either structural similar but syntactically different, or vice versa. Their seems to be three major levels of abstraction which an "optimal" model would need to reach:
1. Syntax similarity - The ability to understand which syntax belongs to which language and how to replace syntax. This seems to be accomplished fairly well and is represented but the much better performance of the Java <-> C++ translations than the python translations.
2. Structural similarity - The ability to understand how code is structured and implemented at a functional level, this is the level the model is likely struggling at and if completed I would expect to see better translation between C++ or Java and Python on functions/shorter code. Additionally at this level the implementations will still likely be identical vs. having only concept similarity.
3. Concept similarity - At this level not only would solid translation between very different languages occur but the structure of the code would change depending on the language. The code would look "native" to its own language vs. just a direct translation.

Below I've written about the training regime and results with some help from the paper figures. My main observations is the ability of the model to bootstrap its own translation learning in the third step of the training regime and that performance favors more complexity -> less complexity which although it would always likely be the case may be mitigated by adding in additional tokens to the training regime vs just removing and shuffling as they did.

## Training Regime

The training regime consists of three separate tasks: language modeling, denoising auto-encoding and back-translation. The important distinction between the denoising step and the language modeling is the introduction of noise beyond masking which are removing and shuffling input tokens (adding tokens does not seem to part of this and i wonder if it was tried, how exactly you would go about adding tokens is not immediately intuitive though). From an architecture perspective they also tweak the XLM model trained in the first task to allow form language to language explicit translation, however, this is not very interesting except as an implementation detail. 

I was surprised that the model learned enough through the first two tasks that they were able to essentially bootstrap a translation training using the model. I refer to it as bootstrapping as the model needs to have some ability to translate before entering the third task or the initial translation step would not work. This part to me is almost the most impressive portion of the paper and the key piece, everything downstream of this task follows and I do not believe their is a higher-level of understanding that would be truly incredible in the final results. It's pretty amazing how far attention and a fairly simple masking/noising regime has brought language tasks.

![Training Regime](https://johncookds.github.io/assets/4/training_regime.png)

## Results

The authors leverage the site [geeksforgeeks.org][geeks4geeks] for testing their model. Not only does the site provide accessible code for relatively simple problems in Python, Java and C++ but crucially the code is designed to look identical across the languages. This simplifies the task and removes the need for a higher-level "concept similarity" from needing to be established. In their results they provide results for different beam search values which predictably significantly improves accuracy. 

Additionally, use of beam search seems something that would be very possible in a use-case. For harder problems I'd even like to see the authors go further and write simple test cases which the output code needs to pass, such a use case strikes me as the most likely implementation when a better model down the line attempts to be actually used in production by someone.

Their is an interesting lack of symmetry in the results with Java -> C++, C++ -> Python and Java -> Python significantly outperforming their counterparts. My best guess for why this is the case is the model may be better at condensing code than expanding code. This idea may not make much since with the Java -> C++ case but if you look at the example below you can see the C++ code often contains changes directly to variables vs the Java code where mediator functions are used. You can get much closer to the C++ code by solely removing characters from the Java code than vice versa. This observation also supports my earlier thought on whether the adding of additional characters in the denoising section was done and just didn't work or was not tried. I would expect if you found a good regime for adding in additional tokens you may get more performance symmetry.

![Results](https://johncookds.github.io/assets/4/results_table.png)

![example code](https://johncookds.github.io/assets/4/example.png)


[paper]: https://arxiv.org/pdf/2006.03511.pdf
[geeks4geeks]: https://www.geeksforgeeks.org/



