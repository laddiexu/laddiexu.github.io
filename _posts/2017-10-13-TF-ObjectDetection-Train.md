---
layout: post
title: "配置运行 Tensorflow Object Detection API "
description: "配置运行 Tensorflow Object Detection API "
category: tech
tags: [DeepLearning, Tensorflow]
modify: 2017-10-30 17:27:00
---

最近在调研物体识别的项目，发现了谷歌开源的基于TensorFlow的一系列模型示例，其中就包括了 [Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection)。
本文主要是记录了我配置以及运行自己的数据集过程和一些注意事项。我也是趟了一些坑，希望对读者有所帮助。

## 配置环境





1. 先需要安装或升级protoc，不然在后面会无法编译。

安装/升级的方法是去protobuf的Release界面：google/protobuf，下载对应的已经编译好的protoc。

环境变量
C:\protoc-3.4.0-win32\bin



3. 
# For CPU   
pip install tensorflow
# For GPU
pip install tensorflow-gpu

sudo pip install pillow
sudo pip install lxml
sudo pip install jupyter
sudo pip install matplotlib


2. 下载代码 
下载tensorflow/models的代码：

git clone https://github.com/tensorflow/models.git

4. 进入到models文件夹，编译Object Detection API的代码：
``` cmd
# From tensorflow/models/
protoc object_detection/protos/*.proto --python_out=.
```




5. install
python setup.py install

whole model

slim

ImportError: No module named 'nets'

则需要进一步安装slim，在models-master\slim目录下也运行
``` python
python setup.py install
```

error: could not create 'build': Cannot create a file when that file already exists



5. 测试成功


#running local with your own data


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

+ [谷歌开放的TensorFlow Object Detection API 效果如何？对业界有什么影响？](https://www.zhihu.com/question/61173908)
+ [目标检测笔记二：Object Detection API 小白实践指南](https://zhuanlan.zhihu.com/p/29215867)
+ [How to train your own Object Detector with TensorFlow’s Object Detector API](https://medium.com/towards-data-science/how-to-train-your-own-object-detector-with-tensorflows-object-detector-api-bec72ecfe1d9)

https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md