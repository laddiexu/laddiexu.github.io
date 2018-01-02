---
layout: post
title: "在 Windows 中配置运行 Tensorflow Object Detection API "
description: "在 Windows 中配置运行 Tensorflow Object Detection API "
category: tech
tags: [DeepLearning, Tensorflow, Object Detection ]
modify: 2017-10-13 17:27:00
---

最近在调研物体识别的项目，发现了谷歌开源的基于 TensorFlow 的一系列模型示例，其中就包括了 [Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection)。
本文主要是记录了我配置以及运行自己的数据集过程和一些注意事项。我也是趟了一些坑，希望对读者有所帮助。

## 配置环境

### 1. 需要安装或升级 protoc，不然在后面会无法编译。

安装/升级的方法是去 protobuf 的 Release界面：[google/protobuf](https://github.com/google/protobuf/releases/)，下载对应的已经编译好的 **protoc** ，比如 protoc-3.4.0-win32.zip。解压后在 bin 目录下有 protoc.exe。
然后可以添加 *C:\protoc-3.4.0-win32\bin* 到环境变量 Path 里。


### 2. 安装或更新 Python 环境和依赖包

这里假设已经安装好了Anaconda。确保安装了以下的一些包：
``` python
# For CPU   
pip install tensorflow
# For GPU
pip install tensorflow-gpu

pip install pillow
pip install lxml
pip install jupyter
pip install matplotlib
```
### 3. 下载代码 

下载 TensorFlow Models 的代码. [TensorFlow Models](https://github.com/tensorflow/models)
``` cmd
git clone https://github.com/tensorflow/models.git
```

### 4. 进入到 *models/research* 文件夹，编译 Object Detection API 的代码：
``` cmd
# From models/research/
protoc object_detection/protos/*.proto --python_out=.
```

### 5. 安装 TensorFlow Model 以及 slim. 也可以直接把 *models/research* 和 *models/research/slim* 添加到 PYTHONPATH.

在 *models/research* 目录下运行以下命令安装 model.
``` python
python setup.py install
```
出现错误 *ImportError: No module named 'nets'* 则需要进一步安装 slim，在 *models/research/slim* 目录下也运行
``` python
python setup.py install
```
如果出现 *error: could not create 'build': Cannot create a file when that file already exists*，把 *slim/build/* 里面对应的生成文件删除重新安装就可以了。

**这里建议添加 PYTHONPATH, 这样就可以修改代码。添加的方法也很简单，只要创建一个环境变量 *PythonPath*, 设置相应的值就可以了。**

已经安装了 package 位于 python 目录下 *Lib\site-packages*。  

### 6. 测试安装是否成功
在 *models/research* 目录下运行以下命令检查安装配置是否成功
``` python
python object_detection/builders/model_builder_test.py
```

如果还有问题，可以参考官方文档 [Installation](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md)



## 运行 Demo

在 *models/research/object_detection* 目录下打开 Jupyter Notebook, 打开文件 *object_detection_tutorial.ipynb*， 直接运行就可以看到测试的结果了。

### 换模型
Tensorflow Object Detection API 里自带了ssd, rfcnn, faster_rcnn 在 COCO 数据集上的 pre-trained 的模型，如下图所示:

![Tensorflow detection model zoo](/assets/20171013/models.png "Tensorflow detection model zoo" )

模型的下载地址为 [Tensorflow detection model zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md)。

在Notebook中，只需要修改 MODEL_NAME 为相应的下载文件夹的名字，就可以尝试不同模型的效果。

![Notebook model name](/assets/20171013/notebook.png "Notebook model name" )

当你需要为自己的数据和问题重新训练模型的时候，这些 pre-train 的模型还可以用来进行迁移学习。

### 换测试图片
如果你想测试 pre-train 的模型在新的图片上的效果，只需要修改 Notebook 中的 *TEST_IMAGE_PATHS*
![Notebook image name](/assets/20171013/notebook-testimage.png "Notebook image name" )


## 参考文献
+ [Tensorflow Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection)
+ [谷歌开放的TensorFlow Object Detection API 效果如何？对业界有什么影响？](https://www.zhihu.com/question/61173908)
+ [目标检测笔记二：Object Detection API 小白实践指南](https://zhuanlan.zhihu.com/p/29215867)
