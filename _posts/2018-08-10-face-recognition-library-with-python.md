---
layout: post
title: "Face Recognition library in python的安装使用"
description: "Face Recognition in python的安装使用, Linux, Windows, GPU"
category: tech
tags: [face recognition, python, 安装, GPU]
modify: 2018-08-10 16:30:00
---

Face Recognition library in python [GitHub:[参考1]](#参考)是一个简单方便的人脸识别的库，支持非深度学习的方法和基于dlib的深度学习的人脸识别方法。

## CPU 
安装配置CPU版本很简单，直接通过pip就可以了。
``` python
pip install dlib
pip install face_recognition
```
在 Windows 上，需要先安装 CMake x64版。

## GPU
要使用GPU支持的版本，需要自己编译一个支持GPU的dlib.当然前提条件是已经安装配置好GPU驱动和cuDNN.

### Linux 
在Linux上编译一个支持GPU的dlib很简单，按照下面的命令直接编译就可以了。[[参考2]](#参考)
```
git clone https://github.com/davisking/dlib.git
cd dlib
mkdir build
cd build
cmake .. -DDLIB_USE_CUDA=1 -DUSE_AVX_INSTRUCTIONS=1
cmake --build .
cd ..
python setup.py install --yes USE_AVX_INSTRUCTIONS --yes DLIB_USE_CUDA
```

在运行 `cmake .. -DUSE_AVX_INSTRUCTIONS=1 -DDLIB_USE_CUDA=1` 后，确保看到日志中出现了 `Enabling CUDA support for dlib.  DLIB WILL USE CUDA` , 表示成功设置成支持GPU版本。

### Windows
在Windows上的配置安装相对麻烦一点,按照以下步骤进行
1. 需要安装 VS 2015 的 C/C++ 编译器，我的实践经验是不支持 VS 2017。
2. 安装 CMake x64 版。
3. 运行以下命令进行编译安装 dlib:
```
git clone https://github.com/davisking/dlib.git
cd dlib
mkdir build
cd build
cmake -G "Visual Studio 14 2015 Win64" -T host=x64 .. -DDLIB_USE_CUDA=1 -DUSE_AVX_INSTRUCTIONS=1
cmake --build .
cd ..
python setup.py install --yes USE_AVX_INSTRUCTIONS --yes DLIB_USE_CUDA
```
4. 用 pip 直接安装 face_recognition 包：
```
pip install face_recognition
```

**如果在 python 代码的 `import face_recognition` 中遇到了 `'Module' object has no attribute 'cnn_face_detection_model_v1'` ,很有可能是你用的 windows 的 Anaconda 环境。
由于 Anaconda 默认安装了一个 dlib 包，并且这个 dlib 包的版本小于 19.5 导致的。解决方法是卸载另外一个 dlib 包：`conda uninstall dlib` 。可以在 Python 中 `print(dlib.__version__)` 来确认dlib版本。**


## 测试GPU
这里以简单的人脸识别为例进行了测试。测试使用的机器是 Azure 上的带 GPU 的虚拟机 NC6。关于 Azure 上带 GPU 的虚拟机的性能等可以参考[这篇文章](https://laddiexu.github.io/tech/2017/12/08/Azure-GPU-VM.html)。
简要来说，配置的 GPU 是 K80, CPU 是 Intel(R) Xeon(R) CPU E5-2690 v3 @ 2.60GHz。以下是用来测试的代码，简单来说就是同一张图片，调用 CPU 方法和 GPU 方法进行识别，运行100次取个平均值。进行的结果是 CPU：0.48s, GPU：0.13s。如果安装配置 GPU 版本不成功，直接运行 testgpu，测试结果为16.99s。（每次运行结果不完全相同，数据仅供参考）。从这个测试结果可以看出，GPU 加速还是有其意义的。

``` python
# testcpu
import face_recognition
import time
sum = 0
N = 100
start = time.time()
for i in range(N):
    image = face_recognition.load_image_file("two_people.jpg")
    face_locations = face_recognition.face_locations(image)
end = time.time()
sum = end - start
print(sum/N)
```

``` python
# testgpu
import face_recognition
import time
sum  =0
N = 100
start = time.time()
for i in range(N):
    image = face_recognition.load_image_file("two_people.jpg")
    face_locations = face_recognition.face_locations(image,number_of_times_to_upsample=0, model="cnn")
end = time.time()
sum = end - start
print(sum/N)
```


## 参考
1. [Face Recognition](https://github.com/ageitgey/face_recognition)
2. [Face recognition with OpenCV, Python, and deep learning](https://www.pyimagesearch.com/2018/06/18/face-recognition-with-opencv-python-and-deep-learning/)



