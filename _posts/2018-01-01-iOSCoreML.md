---
layout: post
title: "Apple的边缘计算 - Core ML"
description: "Apple的边缘计算 - Core ML"
category: tech
tags: [iOS, Core ML, 边缘计算]
modify: 2018-01-01 23:00:00
---

Apple 在WWDC 2017（2017-6-5）上发布的iOS11，其中包含了一些和AI相关的API, 其中一个就是Core ML.
下图是官方对Core ML的解释： 

![Notebook image name](/assets/20180101/coreml0.png "Core ML" )

简单来说，Core ML 就是能够加载机器学习神经网络的模型的一种框架，使用这个框架就可以很方便的在你的app中使用已经训练好的模型了。
从实现上看，Core ML 就是一个模型的转换器，可以将一个Apple自定义的mlmodel格式的模型文件自动生成一些类和方法，这样在app中就可以直接使用这些类去做分析，更简单的使用模型进行预测推断了。

模型从哪里来：
Core ML 

缺点：
1. 从目前的实现来看，Core ML是通过Xcode编译集成到app里面的，所以应该无法实现动态的更新模型，只能通过更新app来更新模型。
2. Core ML定义为支持预测推断阶段，目前无法支持训练阶段，

## 其它API： Vision


![Notebook image name](/assets/20180101/coreml.png "Core ML Architecture" )


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