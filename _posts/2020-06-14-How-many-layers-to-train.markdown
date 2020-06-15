---
layout: post
title:  "Fine-Tuning NLP Tasks - How many layers to train?"
date:   2020-06-13 17:57:47 -0700
categories: thoughts
---

This analysis was done in response to often seeing in posts/code about fine-tuning BERT a "you can either train the classifier or the whole model" dichotomy, without regard for a middle ground(and often not a recognition of the significantly worse performance). I wanted to better understand the relationship between trainable layers, accuracy, training time, and weight drift; at least with respect to this specific scenario. The model used was huggingface's BERT model that has 11 total layers (word embeddings were also included as trainable in the 'Full train' run - which I believe is common practice). The runs marked as 'trained_layers' are layers from the classifier, so 'trained_layers: 6' means the classifier and the last 6 layers(each of which is a transformer) were the trainable parameters.

This is a post of visualizations and from the results attained through running the bert_testing.bat file(which uses BERT_NER.py) in my FineTuningNLPModels folder to test performance and efficiency gains of training various layers of the BERT model during the finetuning process. 


# Loss and Training Time by Trainable Layers

The plots below show decreasing returns to more and more trainable layers and a roughly linearly increase in training time(all models were trained on a GeForce GTX 1070 w/ ~8.5GB). This suggests one should (at least in the NER case(which is a case of highly unbalanced classes)) try to train as many layers as possible. This is quite intuitive but is at least somewhat in contrast to how such questions are treated in example code which often provide the option to train all layers or just train the last layer. For the most part most blog posts/examples do not seem to stress the importance of training more than just the classifier, even if it is just a few extra layers. This importance could also be looked over if just evaluating on the basis of accuracy for imbalanced tasks, as the majority of finetuning tasks are.

The classifier is trained with a higher learning rate(3e-3) than the other runs(3e-5), it is likely that the optimal training rate for the runs with a small number of trained layers is actually somewhere between those two values.

It would be interesting to test how well a BERT model could perform while being iterated through the different fine-tuning tasks with different classifiers but with a certain number of trainable layers during each task as well.

Training Loss

![training loss](https://johncookds.github.io/assets/2/output_6_0.png)

Evaluation Loss

![evaluation loss](https://johncookds.github.io/assets/2/output_7_0.png)

Evaluation F1-Score

![evaluation f1-score](https://johncookds.github.io/assets/2/output_8_0.png)

Training Time

![training time](https://johncookds.github.io/assets/2/output_9_0.png)


# Parameter Drift

If we look below at the base parameter drift for purely the classifier we see much greater parameter drift for just the classifier training than the others. Granted this is very crude and highly dependent on initialization(although multiple runs were tested) and learning rate, but possibly it suggests that too much is being required of the classifier when it is being used as the sole trainable parameter.


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>run</th>
      <th>parameter</th>
      <th>drift</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>classifier</td>
      <td>classifier.weight</td>
      <td>0.021640</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Full</td>
      <td>classifier.weight</td>
      <td>0.000027</td>
    </tr>
    <tr>
      <th>2</th>
      <td>trained_layers: 1</td>
      <td>classifier.weight</td>
      <td>0.000074</td>
    </tr>
    <tr>
      <th>3</th>
      <td>trained_layers: 3</td>
      <td>classifier.weight</td>
      <td>0.000043</td>
    </tr>
    <tr>
      <th>4</th>
      <td>trained_layers: 6</td>
      <td>classifier.weight</td>
      <td>0.000033</td>
    </tr>
    <tr>
      <th>5</th>
      <td>trained_layers: 9</td>
      <td>classifier.weight</td>
      <td>0.000029</td>
    </tr>
  </tbody>
</table>
</div>


