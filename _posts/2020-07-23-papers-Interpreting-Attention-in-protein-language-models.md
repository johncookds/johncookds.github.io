---
layout: post
title:  "Paper - Interpreting Attention in Protein Language Models"
date:   2020-07-22 17:57:47 -0700
categories: papers biology
---

The paper [BERTology Meets Biology: Interpreting Attention in Protein Language Models][paper] analyses a transformer language model pre-trained on protein sequences, in order to detect embedded knowledge of higher-level protein properties. These properties are 3-dimensional structure, binding sites, and contact maps(amino acids close to each other in 3-dimensional space but far apart in the 2-d sequence). The language model encodes the 20 standard amino acids and is pre-trained using a masking technique. Solely the 2-d sequence structure is encoded during the positional embedding and no information about the 3-d structure. They also provide useful [code for creating 3-d protein models visualizing the attention weights][visual_code].

## Attention Analysis

The authors identify specific important tokens(for secondary structure or binding sites) or token-pairs(for contact maps) and look at the proportion of attention that is allocated to those important tokens/interactions. The specifics of how this is done is tied to the index of the important tokens. Thus, for a given layer we look at the attention given from a source_index ('from_index' in their [code][repo]) to a destination_index('to_index' in their [code][repo]). I mention this because I believe it is an open question whether we can interpret this procedure as capturing the 'attention' given to a specific token.

This is especially true in the later layers where we would expect the representation at that index to be very effected by it's previous attentions. Thus, when in the image below they describe the attention "focusing on amino acids *Pro* and *Phe*" I am unsure, beyond the first layer, if the interpretation is even correct and/or meaningful. By the middle layers, the representation at the index of the amino acid *Pro* is so affected by incorporating information from throughout the sequence that to suggest it is still representing the base amino acid seems far-fetched in my view. I do think the interpretation is valid for the first layer and there does seem to be specific attention being paid from a single head to most of the individual amino acids. In response to my critique I expect the authors would argue that there is still something directly related to the base amino acid being captured, the validity of that statement I am unsure of.
*Note the authors do provide similar concerns about using attention in this way in section 5.*

![Attention to specific amino acid indices throughout the network](https://johncookds.github.io/assets/5/amino_attention.png)

The author's presentation below of the attention similarity matrix being quite similar to the corresponding BLOSUM62 scores does provide interesting evidence that the attention layers are capturing important high level features of protein structure. The BLOSUM matrix represents how likely (in log odds ratio terms) a substitution is likely to occur with the corresponding amino acids. A high likelihood suggests these amino acids may function relatively similarly within a protein and thus an attention layer which focuses on a specific amino acid may be more likely to focus on those other amino acids which have a high likelihood of substitution. This does seem to generally be the case.

![Attention Similarity vs BLOSUM substitution score](https://johncookds.github.io/assets/5/attention_blosum.png)


## Probing Tasks

Before continuing with the paper's protein structure, binding sites, and cross-layer results I want to briefly describe the other main tool of analysis the authors' used called "probing tasks."" For each individual probing task the authors build a softmax linear classifier on a layer outputs. The linear classifier is trained to predict the outcome using the token's output vector (for token-level tasks) or the two tokens' element-wise difference concatenated with the tokens' product. For both scenarios the weights of the original model are frozen during the training process. 

## Higher-Level Relationships

### Protein Structure(contact maps)

Beyond the initial analysis discussed above in the "Attention Analysis" section the authors looked at the attention maps and predictive capabilities of their model in terms of contact maps (amino acids close in 3-d space but not in a sequential 2-d space), and binding sites. Their method generally consisted of implementing the "Attention Analysis" described above and choosing the most representative layer-head combination to analyze. Below we can see a single obvious choice for the contact map analysis. The "Attention Analysis" in this case can be done the same as before, however, the relation is between the source_index and the destination_index as opposed to just the destination_index. 

The fact that we have a single attention head in a single layer so clearly representing the contact map strikes me as peculiar. A follow-up analysis that would remove this attention head and see if it had a massive impact on the model's predictive ability would be quite interesting. Because such a specialization did develop I would suspect that the model's ability would be largely impacted and that contact maps are useful for protein sequence prediction. Additionally, the authors' show that the attention weights somewhat map to the probability of contact between the two amino acids.

![Contact-Map Attention Analysis and Classifier](https://johncookds.github.io/assets/5/contactmap.png)

### Binding Sites

The paper next focuses on binding sites and shows the model is more likely to attend to such sites vs. other tokens. They also provide biological reasons why focusing on these binding sites may be useful for amino acid prediction in a sequence. Additionally the increased attention occurs primarily in the later layers which makes sense due to this being a more complex relationship. Interestingly, the map between the attention weights and probability of being a binding site seems to be quite low. From the attention maps we can see a much more concentrated effort to focus on the binding sites as opposed to the contact maps which may suggest greater difficulty in determining and interpreting these binding sites (as compared to the contact maps). Again, this is a case where further analysis into the actual importance of these individual heads would be very useful.

![Binding site Attention Analysis and Classifier](https://johncookds.github.io/assets/5/bindingsite.png)

### Cross-Layer Analysis

In their last section they provide some evidence that local structures (Helix, Turn/Bend, Strand structures) are captured in earlier layers whereas higher-level relationships (binding sites and contact maps) are better captured in later layers. These results are quite hand-wavy and the performance gains throughout the network are not very large for any of the different tasks (it would be helpful to know the evaluation metric at the start vs. just the change in the metric). I expect the contact map and binding site predictions never perform very well. 

An interesting thing to note is that for the three local structures there is a large performance gain at layer 2 and then again at layer 6. At layer 7 we then see the attention really focusing on binding sites (see Figure 6 above). Whether or not this is just a random artifact or represents the moving from local structures to higher-order relationships could possibly be interrogated further (it is also highly unlikely to be modeled so directly in the dichotomous way that I've described).

![Cross-Layer Analysis](https://johncookds.github.io/assets/5/crosslayer.png)

## Final Thoughts

Overall, this paper does a very good job at analyzing the possible understanding of higher-level relationships in transformers models trained on amino acid masking for proteins. I would have liked to see more proof that these relationships were actually important in the prediction task. 

This could be done by removing the information passed through specific attention heads and measuring the change in prediction ability. If the model's performance does not decrease given these experiments, the chance that these higher-level relationships are not actually present or that the interpretive framework does not accurately capture how the model is capturing these relationships would increase in my mind.

Given the relative tiny embedding space when compared to NLP tasks I think it is worth asking whether such a transformer model is too complex for this problem or if it could be altered to better fit this problem space. Some thoughts as to experiments/alterations are below:
1. Reducing complexity by reducing the dimensions of the attention space (the K,Q, and V matrices) or the number of heads. 
2. Increasing the number of layers to see if this could result in better modeling of the higher-level relationships and thus better performance.
3. Explicitly including ideas of 3-dimensional structure either by including a 3-dimensional embedding to the input or adding a 3-dimensional prediction task to the training regime. The other higher-level relationships could be formulated as additional pre-training learning tasks as well(unfortunately in my mind I think this could only be accomplished with labeled data).
4. Are there important factors that are excluded when we tokenize at the amino acid level? Is there a higher-dimensional representation that we could embed protein structure in to capture these factors (this statement is very broad as I'm unsure what exactly this would be).


[paper]: https://arxiv.org/pdf/2006.15222.pdf
[repo]: https://github.com/salesforce/provis/blob/master/protein_attention/attention_analysis/compute_edge_features.py
[visual_code]: https://github.com/salesforce/provis

