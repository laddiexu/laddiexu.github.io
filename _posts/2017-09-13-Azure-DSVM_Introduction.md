---
layout: post
title: "Data Science Virtual Machine 简介"
description: "Data Science Virtual Machine 简介, Azure"
category: tech
tags: [Azure]
modify: 2017-09-13 15:01:00
---
Azure中国 刚刚发布了一个虚拟机的镜像，叫做Data Science Virtual Machine (以下简称 DSVM). 本文就是对DSVM的一个简单的介绍。

## 什么是Data Science Virtual Machine
用一句话来概括，DSVM 是一个综合性的数据科学家云端开发环境。DSVM预装和配置好了各种流行的数据科学相关工具，一键部署，马上可用。下图就是DSVM的一个概览，DSVM包含多种语言，数据平台，开发工具，机器学习和人工智能工具，数据获取的工具，以及数据探索和可视化等六大类的工具和组件。
![组成部分](/assets/20170913/overall.png "组成部分" )

DSVM支持Windows Server和Linux两种系统版本。并且是没有额外的费用的，使用只需要付相应的虚拟机的钱就可以。


## DSVM 典型使用场景
DSVM本质上就是一个虚拟机，所以你能当作普通虚拟机来任意使用。你可以根据项目的需求动态改变虚拟机的大小。你可以选择任意一种语言来进行开始实验。你也可以根据需求安装其他的工具，配置系统等等。
DSVM典型的使用场景包含以下几种：
1. **满足弹性的需求。** 比如数据科学相关的培训，数据科学的变成马拉松，竞赛或者大型数据建模，这些一般都是短时大量的需求，使用DSVM配合虚拟机自定义安装脚本，可以确保一致性的环境，节省评估安装维护各种程序包所需要的时间，节省成本。
1. **项目开始前的实验评估，概念验证环节, Devtest。** DSVM可用于评估或者学习各种工具，进行项目概念验证，沟通实验过程和结果等,开发测试等。
1. **进行深度学习。** DSVM预装有GPU的驱动程序和一些深度学习工具框架，包括CNTK、Tensorflow、MXNet等，配合Azure的带GPU的虚拟机，可以简单快速的进行深度学习。很遗憾目前中国的Azure还没有带GPU的虚拟机，相信很快也会有的。不过DSVM也包含有CPU模式的深度学习框架，部署在不带GPU的虚拟机上，同样可以进行深度学习。


## DSVM中具体包含什么内容
目前中国区上线的是Linux Ubuntu 16.04LTS版本以及Windows Server 2016两个版本。下表是一些重要的组件的列表，需要注意的是，有些组件只有windows版本包含，比如 SQL Server with R Server， PowerBI Desktop. 有些组件只有Linux版本包含，比如Spark本地实例。关于不同版本内集成的软件的完整列表以及最新情况，可以参考[文档](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-virtual-machine-overview)


|功能|组件|Windows版|Linux版|
|---|--|--|--|
|语言工具|Microsoft R| Y| Y|
||Anaconda Python| Y|Y|
||JuliaPro|Y|Y|
||Juypter Notebook|Y|Y|
|机器学习工具| 与 Azure 机器学习集成（R、Python）|Y|Y|
||Xgboost, Vowpal Wabbit|Y|Y|
||Weka, Rattle|Y|Y|
||LightGBM,H2O|N|Y|
|深度学习工具|Microsoft 认知工具包 (CNTK)|Y|Y|
||Tensorflow|Y|Y|
||MXNet|Y|Y|
||Caffe, Caffe2|N|Y|
||Torch|N|Y|
||Theano|N|Y|
|开发工具, DE|Visual Studio 2017|Y|N|
||VS Code|Y|Y|
||RStudio|Y|Y|
||PyCharm|N|Y|
||Vim和Emacs|Y|Y|
||Juno(Julia IDE)|Y|Y|
||Azure SDK|Y|Y|
||Azure 存储资源管理器|Y|Y|
||PowerBI Desktop|Y|Y|
||用于数据浏览的 Apache Drill|Y|Y|
|数据平台工具|SQL Server with R server|Y|N|
||PostgreSQL|N|Y|
||Spark local instance|N|Y|
|||||


## 快速开始
### 创建DSVM
创建DSVM非常简单，使用你的账号登录Azure [portal](https://portal.azure.cn),点击左侧“新建”按钮，在搜索框中搜索 "data science"就会出现Windows版本和Linux版的DSVM,选择需要的版本，按步骤创建就可以了。
![创建 DSVM](/assets/20170913/createvm.png "创建 DSVM" )
### 开始使用 Windows DSVM
创建完成后，就可以像普通的VM一样，使用远程桌面登录DSVM，开始使用。

如果想知道如何运行 Windows 版本提供的特定工具的详细信息，可以参考 [Provision the Microsoft Data Science Virtual Machine](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-provision-vm)

### 开始使用 Linux DSVM
创建完成后，可以使用创建时的用户名密码等信息从SSH客户端登录VM，开始使用。这里推荐用X2Go客户端连接到Linux DSVM。具体步骤如下：
1. 从 [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) 下载安装 X2Go 客户端。 
2. 运行 X2Go 客户端，并选择“新建会话”。 这会打开具有多个选项卡的配置窗口。 输入下列配置参数:
    - 会话选项卡：
    - 主机：主机名或 Linux 数据科学 VM 的 IP 地址。
    - 登录名：Linux VM 上的用户名。
    - SSH 端口：保留默认值 22。
    - 会话类型：将值更改为 **XFCE**。 Linux VM 目前仅支持 XFCE 桌面。

通过 X2Go 客户端使用 SSH 客户端或 XFCE 图形桌面登录 VM 后，即可开始使用 VM 上安装和配置的工具。 在 XFCE 上，可看到许多工具的应用程序菜单快捷方式和桌面图标。


如果想知道如何运行 Linux 版本提供的特定工具的详细信息，可以参考 [Provision the Linux Data Science Virtual Machine](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro)。
