---
layout: post
title:  "Paper - Self-Supervised Scene De-Occlusion"
date:   2020-05-27 17:57:47 -0700
categories: papers computer-vision
---
(post in progress)
## Overview Explanation

This is a blog post about the paper titled [Self-Supervised Scene De-occlusion][project]. The link is to the main project page which contains a very nice overview of their main steps as well as 2 example use cases. The bathroom re-arrangement use case, shown below, was shared on twitter when the overview video first came out. Setting aside any technical details, the authors deserve credit for releasing a summary video along with examples and demos available on their github project repo.

![Bathroom re-ordering](https://johncookds.github.io/assets/1/Self-Supervised_Scene_De-occlusion_summary_video.gif)


The paper takes for granted that the desired objects in the image have all been detected. Thus the input image is not a raw image but the image with appropriate bounding boxes, such as shown below (taken from their example).

![model input example](https://johncookds.github.io/assets/1/model_input.png)

The paper breaks the problem into the following problems:
1. Recover the ordering of objects in the image

Then, for a given target object:

2. Retrieve all the objects blocking('occluding') the target object
3. Generate two images to be used as input to the PCNet-Mask:
    
    A. The first is a black and white image centered on the target image with the target object identified as the target, with the union of the occluding objects are greyed out (and do not need to be identified as different objects)
    
    B. The second image is a rbg image centered on the object with the union of the occluding objects greyed out.
    
    *In the paper, they mention that they were able to both find the amodal mask of the object without identifying the individual occluding objects to the model and able to treat many occluding objects as a single occluder, which they describe as suprising as the training regime they used only uses a single occluder*
    *I suspect that the second image is used as input as they found it led to better amodal(unblocked/ground truth) predictions*

4. Use the trained PCNet-Mask to predict the amodal mask of the target object
5. Generate two images to be used as input to the PCNet-Content:
    
    A. The first is identical to the 1st image as input to PCNet-M except without the occluding objects (and identifies that object as the object to complete)
    
    B. The second uses the PCNet-Mask output and the second image from the input to construct an image with the region where the overlap occured is greyed-out

6. Use the trained PCNet-Content to  predict the unobstructed object

![model/algorithm steps](https://johncookds.github.io/assets/1/overview.png)

## Results and Takeaways

The paper's results seem to suggest that it is the (or one of the) first unsupervised methods for scene de-occlusion. I say this because while they test their method against a supervised method from a [paper published by Facebook AI in 2017] they only use a convex hull approximation as their comparison for the unsupervised case. If not, the choice of such a method to compare against seems weird.

They show that their method outperforms the convex approximations and comes close to the supervised method in amodal completion on two datasets as well as amodal segmentation.

The amodal completion task is essentially just comparing the output of PCNet-M (marked 4 above) to other methods.
The amodal segmentation task is using PCNet-M to generate amodal masks and infer the ordering of the objects.



as they test their method against Results tables from the paper show their model is able to perform about as well as supervised methods, and much better than previous unsupervised tasks.
Broad datasets

Background is not very clear

How do they complete the background after moving objects?

Slimmer dataset may perform better than broad datasets


## Technical Details (to come)

# PCNet Architectures

# PCNet-Mask Training

# PCNet-Content Training

[project]: https://xiaohangzhan.github.io/projects/deocclusion/


