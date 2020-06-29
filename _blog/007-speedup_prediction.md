---
title: "How to reduce the prediction time of deep learning models"
date: 2018-03-13
categories: ["tensorflow", "prediction", "speed optimization"]
collection: blog
---

### When we need speedup the prediction time?

After the model has been trained on GPU servers, we may need to run the model with a very limited resources such as CPU computers or edge devices. It is always complicated because we need running as fast as possible without the loss in performance. Even if we run the model on GPU servers, optimize it is a big things because we can serve more clients and with better experiences (reponse time).

Note: Running prediction on GPU is normally better than CPUs but some new CPUs are quite good compared to old GPUs.

### How to optimize by framework (in computer vision domian)

There are different ways to optimize the deep learning models:

#### PC (usually Intel chipset)

The best option is OpenCV because OpenCV is Intel's product so it optimizes very well for its chipset. OpenCV dnn module only runs on CPU (it can run on GPU now, from version 4.2). OpenCV has also released OpenVINO (https://github.com/opencv/opencv/wiki/Intel's-Deep-Learning-Inference-Engine-backend)

#### Smartphone

OpenCV works fine, OpenVINO does not work on smartphones. OpenCV supports many different formats (tensorflow, caffe, onnx - the good part of onnx is that we can convert many other formats to onnx), however, OpenCV is not the best choice for speed. 

- Speeding up Deep Learning Inference Using TensorFlow, ONNX, and TensorRT (https://devblogs.nvidia.com/speeding-up-deep-learning-inference-using-tensorflow-onnx-and-tensorrt/)
- NCNN of Tencen: https://github.com/Tencent/ncnn works on both CPU and GPU. The Chinese use a lot. 
- Xiaomi's MACE: https://github.com/xiaomi/mace is similar to Tencent's NCNN. They are two best solution from China.
- PytorchMobile of Facebook: https://pytorch.org/mobile/home/. Facebook, after a short period behind Google now has shaped the strategy. It has now PytorchMobile. It covers both CPU and GPU
- TensorflowLITE (LITE, not Tensorflow): by Google, made for the purpose of inference on smartphones (with its own format .tflite).
- There are also a few others. For example, Qualcomm also released a framework to inference deep learning model on her device. Whoever uses this will only run on machines using Qualcomm chips.

#### Mix of edge devices and servers
+ Depending on the situation 

### Optimize by other techniques
In addition, there are other techniques added to speed up the calculation
+ Merge node : For example, when a train model has a conv2D layer, immediately follow by a BatchNorm, after training we can merge these 2 layers to reduce the number of add and mul operations without any loss (s)till bit-exact).
+ Pruning : Search for a little valuable node, least affecting the model, prun it.
+ Post quantification: After training, convert double precision to single precision or convert float to integer. This solution will not be bit-exact but speed will be faster.
+ Quantification: Quantification is done with training (mode quantification). For exemple, we train the parameters which are not float, but integer. Tensorflow supports for this kind of training.

### Some useful techniques
Building network structure of course affects the speed of computation. Again, depending on the condition of resources, we have to design a network that fits with the constraints.
But in most cases, the following conclusions (which have been well tested by the world) are correct
+ ResNet: if there is no short cut in the network -> add it! This does not speed up the calculation, but makes the training much better
+ BatchNorm: if not already, add it!
+ Replace classical conv2D with combination DepthWise + PointWise. This method greatly speeds up calculation and training results are no different from classical conv2D (see MobileNet papers)

+ Tensorflow to train, inference by OpenCV and TensorflowLITE.
For the same network, only use CPU:
- On PC (Intel i9 2.7GHz): OpenCV takes 2ms / frame, TensorflowLITE takes 8ms / frame
- On Smartphones (using Qualcomm 820): OpenCV takes 20ms / frame, TensorflowLITE takes 10ms / frame
This is to know OpenCV is only designed for PCs, and TFLite is for Smartphones! 
