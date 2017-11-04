---
layout: post
title: "使用 Tensorflow Object Detection API 训练自己的模型"
description: "使用 Tensorflow Object Detection API 训练自己的模型"
category: tech
tags: [DeepLearning, Tensorflow, Object Detection ]
modify: 2017-11-04 17:34:00
---

最近在调研物体识别的项目，发现了谷歌开源的基于TensorFlow的一系列模型示例，其中就包括了 [Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection)。
本文主要是记录了我配置以及运行自己的数据集过程和一些注意事项。我也是趟了一些坑，希望对读者有所帮助。

关于如何配置安装参考上一篇文章: [在 Windows 中配置运行 Tensorflow Object Detection API](https://laddiexu.github.io/tech/2017/10/13/TF-ObjectDetection-Train.html)。
本文主要是介绍如何使用 Tensorflow Object Detection API 对自己的数据集进行训练和测试。主要参考了文章 [How to train your own Object Detector with TensorFlow’s Object Detector API](https://medium.com/towards-data-science/how-to-train-your-own-object-detector-with-tensorflows-object-detector-api-bec72ecfe1d9), 以及他的github [Raccoon Detector Dataset] (https://github.com/datitran/raccoon_dataset)。


## 数据标注
当图片收集完成后，首先要进行数据标注。推荐使用 [LabelImg](https://github.com/tzutalin/labelImg) 进行标注，生成的文件是 PASCAL VOC 的 xml 格式。这个工具还可以加载标注文件，检查标注结果。

还需要准备一个 .pbtxt 文件用来说明标注的分类。例如
``` 
item{
    id:1
    name: 'SKU1'
}
```
需要特别注意以下两点：
+ name 不支持直接写中文，需要 UTF-8 编码
+ id 从 1 开始编号，不能从 0 开始编号。

## 数据准备
标注好的数据是图片文件（\*.jpg）和标注文件（\*.xml），而 TensorFlow 不能直接用这些数据作为输入，还需要转成 [TFRecord 格式](https://www.tensorflow.org/api_guides/python/python_io#tfrecords_format_details)。 你可以TFModel代码中的代码*create_pascal_tf_record.py*, *create_pet_tf_record.py*) 来生成 TFrecord 格式，或者参考这个 [Raccoon Detector Dataset] (https://github.com/datitran/raccoon_dataset)，里面有 *xml_to_csv.py* (把xml合并成一个CSV文件)，*split labels.ipynb* (随机划分训练集和测试集)，以及 *generate_tfrecord.py* 生成相对应的 TFRecord 训练集和测试集。我采用的是第二种方式，通过生成的 CSV 文件，还可以用来评估数据量等。

## 在本地训练数据
训练数据可以在本地进行，也可以部署到Google的云上去进行，这里我只尝试了在本地进行训练。

里面最重要的是要配置有一个 pipeline config 文件。具体 config 文件的结构等可以参考文档 [Configuring the Object Detection Training Pipeline](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/configuring_jobs.md)。简单来说，可以去[Tensorflow detection model zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md)下载一个之前 Pre-train 模型, 比如我这里用的是 ssd_mobilenet_v1_coco，然后解压后会看到一个 .config 文件，里面包含有模型的参数，训练的参数，评估的参数等。这里需要修改到的有，
+ 模型参数中的 *num_classes*，改成你的类别数，
+ 训练参数中的 *fine_tune_checkpoint*，采用迁移学习，这里路径的指向刚才下载的Pre-train模型，比如 *ssd_mobilenet_v1_coco_11_06_2017/model.ckpt*
+ *train_input_reader* 下面的 *input_path*，改成你的训练数据，例如 *data/train.record*。*label_map_path*，改成你的 pbtxt 文件路径，例如 *data/object.pbtxt*
+ *eval_input_reader* 下面的 *input_path*，也需要改成你的测试集，例如 *data/test.record*。同样，*label_map_path*，也改成你的 pbtxt 文件路径，例如 *data/object.pbtxt*

如果一切配置正常，运行以下命令就可以开始训练了
``` python
# From the models/research/ directory
python object_detection/train.py --logtostderr --pipeline_config_path=object_detection/training/ssd_mobilenet_v1_toothpaste.config --train_dir=object_detection/traininglog
```

训练过程中可以随时中断训练，在 *train_dir* 里面，会100步左右保存一个模型文件，保留最近的5个模型。最大训练步数也是一个在.config文件里面可以设置的值。


在训练过程中可以在命令行输入以下命令, 随时查看当前 GPU 使用，
```
nvidia-smi
```

## 评估模型
训练结束后，需要对模型进行评估。运行以下的命令。
``` python
# From the models/research/ directory
python object_detection/eval.py --logtostderr --pipeline_config_path=object_detection/training/ssd_mobilenet_v1_toothpaste.config --checkpoint_dir=object_detection/traininglog --eval_dir=object_detection/eveldire
```

然后在 Tensorboard 中就可以看到评估的结果了。
``` python
tensorboard --logdir=object_detection/eveldire
```

## 导出和使用模型
评估完成后，假设需要把这个模型导出，然后在其他地方使用这个模型，只需要运行

``` python
# From the models/research/ directory
python object_detection/export_inference_graph.py --input_type image_tensor --pipeline_config_path=object_detection/training/ssd_mobilenet_v1_toothpaste.config --trained_checkpoint_prefix=object_detection/newmodel/model.ckpt-6674 --output_directory object_detection/newmodel/output_inference_graph.pb
```
之后就可以看到 *output_inference_graph.pb* 就是导出的模型了。
需要注意的是，参数中的 *--trained_checkpoint_prefix* 是需要指定到单个模型的，例如 *model.ckpt-6674*，这个 6674 就是训练了 6674 步后自动保存模型。

在目录 *models/research/object_detection* 下，有一个Jupyter Notebook: *object_detection_tutorial.ipynb*。可以通过修改Notebook 里面读取模型的位置，来测试使用模型。


## 参考
+ [Tensorflow Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection)
+ [谷歌开放的TensorFlow Object Detection API 效果如何？对业界有什么影响？](https://www.zhihu.com/question/61173908)
+ [目标检测笔记二：Object Detection API 小白实践指南](https://zhuanlan.zhihu.com/p/29215867)
+ [How to train your own Object Detector with TensorFlow’s Object Detector API](https://medium.com/towards-data-science/how-to-train-your-own-object-detector-with-tensorflows-object-detector-api-bec72ecfe1d9)
+ [Raccoon Detector Dataset](https://github.com/datitran/raccoon_dataset)