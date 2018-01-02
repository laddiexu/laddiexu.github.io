---
layout: post
title: "Apple的“边缘计算” - Core ML"
description: "Apple的边缘计算 - Core ML"
category: tech
tags: [iOS, Core ML, 边缘计算]
modify: 2018-01-01 23:00:00
---

## Core ML
Apple 在 WWDC 2017（2017-6-5）上发布的 iOS11，其中包含了一些和机器学习和人工智能相关的 API, 其中一个就是 Core ML。
下图是官方对 Core ML的解释 [[参考1]](#参考)： 

![Notebook image name](/assets/20180101/coreml0.png "Core ML" )

简单来说，Core ML 就是能够加载机器学习神经网络的模型的一种框架，使用这个框架就可以很方便的在你的 app 中使用已经训练好的模型了。
从实现上看，Core ML 就是一个模型的转换器，可以将一个 Apple 自定义的 mlmodel 格式的模型文件自动生成一些类和方法，这样在 app 中就可以直接使用这些类去做分析，更简单的使用模型进行预测推断了。

Core ML 屏蔽了底层的物理实现细节，甚至也屏蔽了模型的细节和参数，app 开发者只要用 iOS 的开发经验传入参数，调用预测，就可以拿到结果了。不需要关心是 CPU 计算还是 GPU 的计算，不需要关心是神经网络的模型还是 SVM 的模型。

之所以称 Core ML 是 Apple 的边缘计算方案，是由于 Core ML 实际上是把整个模型放在手机端进行计算的，使用过程中不需要联网，是完全的在设备端计算。

### 模型从哪里来？
Apple 提供了一些已经编译成 mlmodel 格式的模型，主要是学术界图片分类的模型，包括：MobileNet,ResNet50，Inception V3, VGG16 等。更重要的是，Apple 开发和开源了一个 Python 工具包，支持把基于常用的机器学习算法库的模型转化为 mlmodel 格式，目前支持的有深度学习的 Keras, Caffe，机器学习中常用的 scikit-learn, libsvm, XGBoost。 除此之外，还有针对深度学习领域的热门框架TensorFlow, MXNet，有专门的 Converter。当然还有 Apple 自己的模型开发工具 Turi Create。基本已经覆盖了当前业界常用的 Python 机器学习库。

### 支持的设备？
Core ML 不只是在 iOS 中支持，还包含在 macOS，tvOS，watchOS 里。所以理论上讲，同样的模型可以在手机，平板，电脑，电视盒子，甚至在手表上运行。

### 计算的性能？
神经网络的模型，即使是在预测推断阶段，计算量也是比较大的。对于设备的硬件要求其实是挺高的，我特别去查了下 iPhone 的 GPU 的计算能力。结果如下：

| 设备 | 芯片 | FLOPS |
|------|---|---|
| AWS DeepLens | |100G|
| iPhone 6, 6P | A8 | 136.5G |
| iPhone 6S, 6SP | A9 | 230.4G |
| iPhone 7, 7P | A10 Fusion | 250G |
| iPhone 8, 8P, X | A11 Bionic | 600G |

+ AWS DeepLens 是 AWS 2017-11-30 发布的支持深度学习的摄像机。[[参考3]](#参考)
+ Apple 在最新的A11芯片中，除了CPU,GPU 外，还单独设计了一个 Nerual Engine，专门用来做神经网络的计算。官方宣称的 Nerual Engine 可以达到 600 GFLOPS。[[参考5]](#参考)

对比在我的上一篇文章[Azure上带GPU的虚拟机的性能](https://laddiexu.github.io/tech/2017/12/08/Azure-GPU-VM.html)中写道的，Intel Xeon E5-2673 v3 (Haswell) 也才只有 500GFLOPS 左右，可见 iPhone8 的神经网络计算能力已经和 PC 相当了。当然这个只是理论上，实际使用中的结果受多种因素的影响。


### 缺点：
* 从目前的实现来看，Core ML 是通过 Xcode 编译集成到 app 里面的，所以应该无法实现动态的更新模型，只能通过更新 app 来更新模型。
* Core ML 定义为支持预测推断阶段，从目前的实现来看，缺乏训练需要的一些函数实现（主要指神经网络），没有办法实现在本地训练。
* Core ML 是单独的设备端计算方案，没有和云端的传输部署等支持。这里对比的就是 AWS DeepLens 和 Azure IoT Edge 的概念。后两者由于有云计算平台的支持，集成了云平台的一些服务，更重要的是可以实现在云平台训练模型，一键部署到设备中。是边缘+云的计算。



## 其它机器学习API，Vision 等
WWDC 2017 上，除了Core ML 外，Apple 还发布了一系列其他的 API，比如 Vision。从下图的架构图也可以看出来，Vision 等机器学习的API 底层用的还是 Core ML。

![Notebook image name](/assets/20180101/coreml.png "Core ML Architecture" )

 一句话来理解，就是带了 Apple 自己模型的特定功能的 API，有点像 Cognitive Service 的离线版本。以 Vision API 为例，包含有以下几种功能：

* 人脸检测
* 二维码/条形码检测
* 文字检测
* 水平线识别
* 其他图像处理

Vision API 可以与 CoreML 结合起来，使用 Vison API 调用自己的模型进行图像识别。如果有兴趣进一步了解Vision API的使用，可以参考 [[参考7，8]](#参考)


## 参考
1. [Apple Machine Learning](https://developer.apple.com/machine-learning/) 
2. [Core ML 官方文档](https://developer.apple.com/documentation/coreml)
3. [AWS DeepLens](https://aws.amazon.com/cn/deeplens/)
4. [Apple designed processors](https://en.wikipedia.org/wiki/Apple-designed_processors)
5. [Apple A11](https://en.wikipedia.org/wiki/Apple_A11)
6. [GPU FLOPS](http://kyokojap.myweb.hinet.net/gpu_gflops/)
7. [Core ML and Vision Framework on iOS 11](http://yulingtianxia.com/blog/2017/06/19/Core-ML-and-Vision-Framework-on-iOS-11/)
8. [WWDC 2017 - Vision 图像识别框架的使用](http://www.cocoachina.com/ios/20170801/20061.html)
