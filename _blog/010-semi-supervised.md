---
title: "Deep learning basing on EM"
date: 2018-11-20
categories: ["semi-supervised", "weakly supervised", "deep learning"]
collection: blog
---


![learning settings](/images/semi_supervised/learnings.png)
Training  settings  for  object  detection  using  CNN  models.  (a)  is  supervisedlearning  which  uses  instance-level  labels  (red  bounding  boxes  in  the  image).  (b)  isweakly-supervised learning where only image-level labels (”train”) are used. (c) is mix-supervised learning where both image-level labels (”train”) and instance-level labels(red  bounding  boxes)  are  used.  (d)  is  semi-supervised  learning  where  instance-levellabeled images and unlabeled images are used


## I. Expectation Maximization

We need to understand how the EM algorithm works (see ![previous post](https://nhuvan.github.io/blog/09-EM/)).

## II. Basic deep learning with EM approach.

The approach is based on the EM principes. 
+ We start by training an initial deep learning model such as an image classifier or an object detector
+ EStep: We estimate the labels of the unlabeled data in semi-supervised learning or (the missing labels in weakly-supervised learning) on the interval of T iterations
+ MStep: Using the existing labels and the newly estimated labels as Ground-Truth, we can continue to train the model with a new a specific or an dynamic weighting loss function and training schema  (the importance of estimated labels are normaly less than the existing labels)
+ Estep and MStep is repeated with the interval T iterations.

Note that the pseudo-label approach often using in competitions as Kaggle is the naive version of this method.