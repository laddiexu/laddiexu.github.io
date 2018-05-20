---
layout: post
title: "微软的的“边缘计算” - Azure IoT Edge"
description: "微软的边缘计算 - Azure IoT Edge"
category: tech
tags: [边缘计算, intellegent edge ]
modify: 2018-05-10 23:00:00
---
在刚刚过去的 Microsoft Build 2018上面(西雅图时间5月7日-5月9日)[[参考1]](#参考)，微软隆重介绍了“边缘计算”(Intelligent Edge)中的一个重要组成服务 Azure IoT Edge. 本文是对 Azure IoT Edge 做一个简单的介绍。

## 什么是 Azure IoT Edge
首先，Azure IoT Edge 并不是一个独立的服务，它是和 Azure IoT Hub 绑定在一起，结合使用的 Azure 服务。主要是帮助用户把原来运行在云端的分析和业务模块轻松部署在设备端。它是一套软件平台，在设备端是基于容器技术(Container)来实现的。所以 Azure IoT Edge 能够跨平台，支持 Windows 和 Linux 操作系统。对硬件要求上来说，需要有能运行 Docker 的操作系统。目前官方宣布支持的设备有树莓派(Raspberry Pi 3)，大疆无人机，以及高通的一款摄像头。后两者都是在 Build Keynote 上演示过的设备。未来更多的设备可以查询 Azure 认证的 IoT 设备目录[[参考3]](#参考)

## Azure IoT Edge的组成
Azure IoT Edge 主要包含以下三个部分：
1. IoT Edge 模块(IoT Edge Modules)：
  IoT Edge 的模块其实就是一个 Docker 的 Container。在一个 Edge 设备上可以运行多个模块，模块和模块之间可以进行通信，从来实现数据分析，业务逻辑等的处理。模块和模块之间是相对独立的，甚至可以用不同的语言实现不同的模块，比如用 C# 实现业务逻辑，用 Python 实现数据分析的逻辑等。Azure 的很多服务都可以简单的打包成一个模块部署到 Edge 设备上，比如 Azure Functions, Azure Stream Analytics, Azure Machine Learning。特别是使用 Azure Machine Learning 服务的话，就可以轻松实现云上训练模型，设备上离线使用模型，动态更新模型等场景。
  
2. IoT Edge 运行时(IoT Edge Runtime):
  IoT Edge 运行时也是安装在 Edge 设备上的，主要是负责对 Edge 模块的管理和通信功能的。下图就是 Edge Runtime 的位置和功能的示意图。
  ![Notebook image name](/assets/20180509/runtime.png "IoT Edge Runtime" )
  IoT Edge Runtime 其实就是两个 Container,一个叫 EdgeAgent， 一个叫 EdgeHub。 EdgeAgent 主要负责管理模块，包括下载，启动，删除 Edge 模块，监控和汇报健康状态等。EgdeHub 主要负责通信，包括模块和模块之间的通信，Edge 设备和云端的通信等。微软计划在正式发布之后会开源 Edge 运行时的代码。

3. IoT Edge 云端接口(IoT Edge cloud interface):
  IoT Edge 的云端接口集成在 IoT Hub 服务当中，用来远程监控和 IoT Edge 的设备。以及动态部署 Edge 模块到设备上。下图就是云端接口的位置和功能的示意图。
  ![Notebook image name](/assets/20180509/cloud-interface.png "IoT Edge Cloud Interface" )

## AI on Azure IoT Edge
IoT Edge 的智能能力体现的 Edge 模块上。其实上面的模块介绍中也提到了，Edge 的模块有两大类，一类是用户完全自己编写的，另一类是打包 Azure 的一些服务。用户完全自己编写的模块，当然可以用任何语言，任何模型实现用户自己的模型算法。微软即将推出一个 Edge 模块市场(Module Marketplace)，也是一种帮助用户找到合适模块的方案。打包 Azure 的一些服务里面，除了上文提到的 Azure Stream Analytics, Azure Machine Learning 可以帮助用户获得智能模型外，还有一个重要的服务 Azure Cognitive Service。目前认知服务中的自定义影像服务(Custom Vision Service)已经能够支持导出训练好的模型为 Docker Container了。而且自定义影像服务不仅是支持图像分类场景，也支持了物体识别的场景。相信会有更多的 Azure 智能服务支持 Azure IoT Edge 的。


## 参考
1. [Microsoft Build 2018 keynotes](https://developer.microsoft.com/en-us/events/build/keynotes)
2. [Azure Certified for IoT device catalog](https://catalog.azureiotsolutions.com/)
3. [What’s new with Azure IoT Edge?](https://azure.microsoft.com/en-us/blog/what-s-new-with-azure-iot-edge/)
4. [Azure IoT Edge](https://azure.microsoft.com/en-us/services/iot-edge/)


