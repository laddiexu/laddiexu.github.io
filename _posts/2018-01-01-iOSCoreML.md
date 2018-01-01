---
layout: post
title: "一分钟读懂Apple的边缘计算"
description: "一分钟读懂Apple的边缘计算"
category: tech
tags: [Azure, GPU, Nvidia]
modify: 2018-01-01 23:00:00
---
Appl
AI是目前所有公司都不能回避的话题了，有公司选择做云，有公司选择做开发工具，

Apple 在WWDC 2017（2017-6-5）上发布的iOS11，其中包含了一些和AI相关的API, 特别是Core ML.下图是官方对Core ML的解释：
![Notebook image name](/assets/20180101/coreml0.png "Core ML" )
简单来说，CoreML就是能够加载训练好的模型的一种框架，使用这个框架就可以很方便的在你的app中使用机器学习神经网络的模型。Core ML可以看做一个模型的转换器，可以将一个 ML Model 格式的模型文件自动生成一些类和方法，可以直接使用这些类去做分析，让我们更简单的在app中使用训练好的模型。
Core ML支持的模型必须是Apple自己定义的mlmodel格式。






http://yulingtianxia.com/blog/2017/06/19/Core-ML-and-Vision-Framework-on-iOS-11/

https://github.com/yulingtianxia/Core-ML-Sample

http://www.cocoachina.com/ios/20170801/20061.html

https://www.anandtech.com/show/9686/the-apple-iphone-6s-and-iphone-6s-plus-review/5
https://www.quora.com/How-many-GFLOPS-of-floating-point-performance-does-Apples-A10-Fusion-output
https://www.anandtech.com/show/11835/apple-2017-the-iphone-x-ten
http://www.fudzilla.com/news/graphics/44487-apple-s-a11-has-apple-gpu
https://en.wikipedia.org/wiki/Apple-designed_processors
https://en.wikipedia.org/wiki/Apple_A11 600G
http://kyokojap.myweb.hinet.net/gpu_gflops/

？？？
convert a neural network model's floating point weights from full-precision into half-precision values (reducing the number of bits used in the representation from 32 down to 16). This type of conversion can significantly reduce a network's size, most of which often comes from the connection weights within the network.