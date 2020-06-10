---
layout: post
title:  "Paper - Self-Supervised Scene De-Occlusion"
date:   2020-05-28 17:57:47 -0700
categories: papers computer-vision
---

This is a review of the paper titled [Self-Supervised Scene De-occlusion][project]. The link is to the main project page which contains a very nice overview of their main steps as well as 2 example use cases. The bathroom re-arrangement use case, shown below, was shared on twitter when the overview video first came out. The sheer amount of material released alongside their paper (summary video along with examples and demos available on their github project repo) deserves a lot of credit.

![Bathroom re-ordering](https://johncookds.github.io/assets/1/Self-Supervised_Scene_De-occlusion_summary_video.gif)


The paper takes for granted that the desired objects in the image have all been detected. Thus the input image is not a raw image but the image with appropriate bounding boxes, such as shown below (taken from their example).

![model input example](https://johncookds.github.io/assets/1/model_input.png)

The paper breaks the problem into the following problems:
1. Recover the ordering of objects in the image using the PCNet-Mask
2. Retrieve all the objects blocking('occluding') for a given target object
3. Generate two images to be used as input to the PCNet-Mask:
    
    A. The first is a black and white image centered on the target image with the target object identified as the target, with the union of the occluding objects are greyed out (and do not need to be identified as different objects)
    
    B. The second image is a rbg image centered on the object with the union of the occluding objects greyed out.
    
    *In the paper, they mention that they were able to both find the amodal mask of the object without identifying the individual occluding objects to the model and able to treat many occluding objects as a single occluder, which they describe as suprising as the training regime they used only uses a single occluder. Additionally, I suspect that the second image is used as input as they found it led to better amodal(unblocked/ground truth) predictions (this is also the information that the convex approximation in their results would not have access to, so it would be interesting to remove this image from the input and compare the results)*

4. Use the trained PCNet-Mask to predict the amodal mask of the target object
5. Generate two images to be used as input to the PCNet-Content:
    
    A. The first is identical to the 1st image as input to PCNet-M except without the occluding objects (and identifies that object as the object to complete)
    
    B. The second uses the PCNet-Mask output and the second image from the input to construct an image with the region where the overlap occured is greyed-out

6. Use the trained PCNet-Content to  predict the unobstructed object

![model/algorithm steps](https://johncookds.github.io/assets/1/overview.png)

## Results and Takeaways

The paper's results seem to suggest that it is the (or one of the) first unsupervised methods for scene de-occlusion. I say this because while they test their method against a supervised method from a [paper published by Facebook AI in 2017][facebook_paper] they only use a convex hull approximation as their comparison for the unsupervised case. If not, the choice of such a method to compare against seems weird.

They show that their method outperforms the convex approximations and comes close to the supervised method in amodal completion on two datasets as well as amodal segmentation.

The amodal completion task is essentially just comparing the output of PCNet-M (marked 4 above) to other methods.
The amodal segmentation task is using PCNet-M to generate amodal masks and infer the ordering of the objects.
They do not offer metrics on which to evaluate their PCNet-C or scene fidelity.

The main takeaway from the paper is that we can find the amodal mask of objects in a self-supervised fashion. I am also curious if with a more uniform dataset (such as the images being of room arrrangements) the mask inference could be improved, as most likely any industry use case would focus on an image set much less diverse than the COCOA or FINS dataset they use. Such a dataset may also help with both the content completion task and  the image inpainting task(which is required after repositioning an object in the image). One could also apply a similar self-supervised masking idea to the background completion/image inpainting component as well (I would not be suprised if this has already been done).

From this paper, my next step would be to look into the super-resolution literature and hope to improve on the content completion and image inpainting(background completion) tasks, the latter being especially important for the object manipulation in a scene use case. The ability to maintain a stable and high-fidelity background seems as though it would be the main hurdle to implementing a use-case such as the first gif for customers.


## Technical Details

# PCNet Architectures

The PCNet architectures are based on the [UNet architecture][unet_paper], shown below. It provides pixel-by-pixel image segmentation, the pixel specific nature is obviously useful for this setting. Whether or not a different network would perform better is an open question, the UNet architecture is fairly shallow/fast so potentially a deeper network may be able to perform better.

The Unet architecture essentially has a convolution down-sampling left side into a bottleneck and a de-convolution up-sampling right side. The distinguishing feature is that each convolutional block output is both passed to the next convolutional block and passed across to the corresponding de-convolutional block. This masssively open ups the degrees of freedom on which the model can fit. It seems that this may disrupt the ability of the bottleneck to encode useful information, if significant information is being "passed over" the bottleneck but it may be that the bottleneck cannot encode enough information to perform a pixel by pixel task.

The source code can be found in their github repository from their [project site][project].

![UNet Architecture](https://johncookds.github.io/assets/1/unet_architecture.png)

# PCNet-Mask Training

The authors find that they do not an amodal instance of the object to recreate it's mask and can actually effectively reproduce the mask by training their network on further occluded instances of the desired object. This is shown in training case 1 below. The "surrogate object" is nothing more than a mask to further occlude the partially occluded object and train the PCNet-Mask to reproduce the partially occluded object (this paradigm is why it is labeled as self-supervised and not un-supervised). Note that in the car example below, the target car is being partially occluded in front for every image. They also need to implement a regularizing case such that the network does not learn to always add to the modal mask(the mask when the object is occluded) and thus places the object in front of the "surrogate object" for this case. For each case the target mask is the same (its also important to remember the PCNet-M is not given any indication of which object is in front of which).

![PC Regularization](https://johncookds.github.io/assets/1/pc_regularization.png)

![PCNet-Mask Training](https://johncookds.github.io/assets/1/pcnetm_training.png)

# Ordering Recovery

The PCNet-Mask is actually first used to find the correct ordering graph. After being trained, the PCNet-Mask is used to test whether two objects are overlapping and if they are which are in front of the other. This is done be creating two inputs to the PCNet-M with each object marked as the target object, because of the regularizer in the training process if the target object is not occluded it will be reproduced and if it is occluded it will an amodal mask will be generated. Then for each case the generated mask can be compared to the original modal mask, and the masks which are more similar represent the object that is not occluded (in the case two objects don't actually occlude it a threshold can be applied to the decision function).

![Ordering Recovery](https://johncookds.github.io/assets/1/ordering_recovery.png)

# PCNet-Content Training

PCNet-Content is trained with a similar regime to PCNet-M where their is a target object(which may be partially occluded already), it is then further occluded and the occluded area is meant to be represented by the model.

![PCNet-Content Training](https://johncookds.github.io/assets/1/pcnetc_training.png)

# References

[Self-Supervised Scene de-occlusion project][project]

[Unet paper][unet_paper]

[Facebook AI supervised segmentation paper][facebook_paper]


[project]: https://xiaohangzhan.github.io/projects/deocclusion/
[facebook_paper]: https://arxiv.org/pdf/1509.01329v1.pdf
[unet_paper]: https://arxiv.org/pdf/1505.04597.pdf


