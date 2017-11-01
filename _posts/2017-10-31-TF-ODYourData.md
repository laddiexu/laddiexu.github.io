---
layout: post
title: "在 Windows 使用 Tensorflow Object Detection API 训练自己的模型"
description: "在 Windows 使用 Tensorflow Object Detection API 训练自己的模型"
category: tech
tags: [DeepLearning, Tensorflow, Object Detection ]
modify: 2017-10-31 17:58:00
---

最近在调研物体识别的项目，发现了谷歌开源的基于TensorFlow的一系列模型示例，其中就包括了 [Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection)。
本文主要是记录了我配置以及运行自己的数据集过程和一些注意事项。我也是趟了一些坑，希望对读者有所帮助。

关于如何配置安装参考上一篇文章: [在 Windows 中配置运行 Tensorflow Object Detection API](https://laddiexu.github.io/tech/2017/10/13/TF-ObjectDetection-Train.html)。
本文主要是介绍如何使用 Tensorflow Object Detection API 对自己的数据集进行训练和测试。主要参考了文章 [How to train your own Object Detector with TensorFlow’s Object Detector API](https://medium.com/towards-data-science/how-to-train-your-own-object-detector-with-tensorflows-object-detector-api-bec72ecfe1d9), 以及他的github [Raccoon Detector Dataset] (https://github.com/datitran/raccoon_dataset)。



## 数据标注
当图片收集完成后，首先要进行数据标注。推荐使用 [LabelImg](https://github.com/tzutalin/labelImg) 进行标注，生成的文件是 XML 的，PASCAL VOC 格式的。


## running local with your own data


A local training job can be run with the following command:
``` python
# From the tensorflow/models/research/ directory
python object_detection/train.py \
    --logtostderr \
    --pipeline_config_path=${PATH_TO_YOUR_PIPELINE_CONFIG} \
    --train_dir=${PATH_TO_TRAIN_DIR}
```
where ${PATH_TO_YOUR_PIPELINE_CONFIG} points to the pipeline config and ${PATH_TO_TRAIN_DIR} points to the directory in which training checkpoints and events will be written to. By default, the training job will run indefinitely until the user kills it.

``` python
python object_detection/train.py --logtostderr --pipeline_config_path=object_detection/training/ssd_mobilenet_v1_toothpaste.config --train_dir=object_detection/traininglog

```


tranfer learning
https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/configuring_jobs.md

https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md




GPU monitor commond
For Nvidia GPUs there is a tool nvidia-smi that can show memory usage, GPU utilization and temperature of GPU. 


## 参考
参考了以下一些博客文章回答等等，在这里一并表示感谢：

+ [Tensorflow Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection)
+ [谷歌开放的TensorFlow Object Detection API 效果如何？对业界有什么影响？](https://www.zhihu.com/question/61173908)
+ [目标检测笔记二：Object Detection API 小白实践指南](https://zhuanlan.zhihu.com/p/29215867)
+ [How to train your own Object Detector with TensorFlow’s Object Detector API](https://medium.com/towards-data-science/how-to-train-your-own-object-detector-with-tensorflows-object-detector-api-bec72ecfe1d9)
+ [Raccoon Detector Dataset] (https://github.com/datitran/raccoon_dataset)