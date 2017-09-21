---
layout: post
title: "如何在 Azure 上快速创建 Microsoft R Server"
description: "Microsoft R Server 创建，环境设置, Azure"
category: tech
tags: [Azure]
modify: 2017-09-20 19:17:00
---

## 什么是 Microsoft R
R 语言一套完整的数据处理、计算和制图软件系统。R 内置多种统计学及数字分析功能,也可以通过安装包增强。R 是属于 GNU 系统的一个自由、免费、源代码开放的软件，可在多种平台下运行，是目前最火的统计和机器学习工具之一。
Microsoft R 提供了一系列服务器和工具来填补 R 在企业部署中的功能空白。Microsoft R 产品系列构建在开源 R 之上，以 Microsoft R Server，Microsoft R Client 和 Microsoft R Open 三种形式提供免费和商业的产品。 除了可用于所有 R 用户的超过10,000个标准 R 包之外，Microsoft R Server 和 R Client 还包含额外的 R 包和连接工具，可通过用于实现远程计算环境和远程执行，Web 服务部署，机器学习集成和可扩展的基于集群的解决方案。
|产品名称|功能|
|---|--|
|Microsoft R Open|是微软分发的开源的R，是一个完全开源的平台。以前称为 Revulition R Open (RRO)。包含在 Microsoft R Client 和 Microsoft R Server 中|
|Microsoft R Client|是一个免费的工作站版本。包括了R Server 相同的功能，但是只能用于本地|
|Microsoft R Server|是 Microsft R 的旗舰产品。能够支持企业中非常大的负载需求|
|||


## 什么是 Microsoft R Server
上文已经介绍了 Microsoft R Server 是 Microsoft R中的旗舰产品，能够支持企业中非常大的工作负载。 R Server 用于运行，分配和管理 R 进程，使其能够分布式的运行在多台服务器（无论是 Linux 还是 Windows）或者是集群（Hadoop 和 Spark）上。R Server 提供了一个执行引擎来支持基于 Microsoft R 包的解决方案。这个解决方案扩展了开源的 R，能够支持高性能分析，统计分析，机器学习场景和大规模数据集。这些增值功能是用与 R Server 一起安装的专有软件包的方式来提供的。
R Server 支持多种平台，目标是实现数据在哪里，计算就在哪里。目前的支持的平台有如下几种：

|平台|安装|快速开始|
|---|--|--|
|R Server for Linux| [文档](https://msdn.microsoft.com/en-us/microsoft-r/rserver-install-linux-server) | [文档](https://msdn.microsoft.com/en-us/microsoft-r/scaler-getting-started-data-import-exploration) |
|R Server for Windows| [文档](https://msdn.microsoft.com/en-us/microsoft-r/rserver-install-windows) | [文档](https://msdn.microsoft.com/en-us/microsoft-r/scaler-getting-started-data-import-exploration) |
|R Server for Hadoop| [文档](https://msdn.microsoft.com/en-us/microsoft-r/rserver-install-hadoop) | [文档](https://msdn.microsoft.com/en-us/microsoft-r/scaler-hadoop-getting-started) |
|SQL Server Machine Learning Service| [文档](https://msdn.microsoft.com/library/mt696069.aspx) | [文档](https://msdn.microsoft.com/library/mt604885.aspx) |
||||

## 如何在 Azure 上快速创建 Microsoft R Server
R Server 可以直接在你的服务器上安装使用，也可以在 Azure 上使用。

在 Azure 上创建预装好 R Server 的虚拟机非常简单，使用你的账号登录 Azure [门户网站](https://portal.azure.cn), 点击左侧“新建”按钮，在搜索框中搜索 " R server "就会出现多个版本的 R Server，选择需要的版本，按步骤创建就可以了。我们这里选了 R Server Only 的 Windows 版本。
![创建 R Server](/assets/20170920/CreateRServer.png "创建 R Server" )

创建成功之后就是一个装好了 R Server 的 Windows Server 2016 虚拟机，可以远程连接到虚拟机上开发测试 R 程序了。

## 如何配置 Microsoft R Server
在创建成功之后，还需要对 R server 进行配置。
R Server 有两种配置模式：
- **企业配置**：指的是把提供 Web 服务节点和提供计算节点配置到不同的机器上。这种配置可以通过增加或者删除节点来实现可扩展性。关于企业配置的方法本文不会涉及，可以参考[文档](https://docs.microsoft.com/en-us/r-server/install/operationalize-r-server-enterprise-config)。
- **单一( One-box )配置**: 就是把 Web 节点和计算节点运行在同一个机器上。当然没有任何可扩展性。适合于测试，概念验证，原型构建等环节，但是不适合产品环境。本文介绍的是这种配置。

![One-box 配置架构图](/assets/20170920/setup-onebox.png "One-box 配置架构图" )

由于前一步我们创建的是 Windows 版本的 R Server, 下面的配置版本也以 Windows 版本为例，Linux 版本的配置可以参考[文档](https://docs.microsoft.com/en-us/r-server/install/operationalize-r-server-one-box-config)

配置的具体步骤如下：
1. 在开始菜单中找到 **R-Server-Admin-Util** ,右键使用管理员权限打开。

    ![Admin Util](/assets/20170920/admintool.png "Admin Util" )
2. 按照步骤和指示，选择 **Configure R Server for Operationalization**，**One-box**，设置你的管理员密码，注意密码的复杂性要求。最后会进入配置阶段，当弹出需要密码时，输入刚才设置的密码就可以了。

   ![Config](/assets/20170920/config.png "Config" )

3. 显示配置完成后，需要进行一下测试。测试的方法与配置类似，首先回到主菜单，然后根据步骤和指示，依次选择**Run diagnostic tests**，**Test configuration**, 输入管理员密码。最后可以看到测试结果整体为 **pass**，说明已经配置成功了。
 
   ![Test Config](/assets/20170920/testConfig.png "Test Config" )


