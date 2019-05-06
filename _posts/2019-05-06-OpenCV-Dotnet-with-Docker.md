---
layout: post
title: "在 Docker 中使用 OpenCV 和 .NET"
description: "在Docker中使用OpenCV和.NET, .NET Core和.NET Framework, windowsservercore"
category: tech
tags: [OpenCV, dotnet, .NET, Docker, windows]
modify: 2019-05-06 00:00:00
---
之前我们用 OpenCV 做了一些视频图像分析的项目，用 Docker 进行了部署，遇到过一些小问题，在这里简单记录一下，希望能帮助到需要的人吧。
我们使用的语言是 C#, 尝试了 Windows docker + .NET Framework 和 Linux docker + .NET Core 方案。首先解释下以下几个基本概念：

## .NET Framework, .NET Core 和 .NET Standard
.NET 是一个庞大的家族，包含有很多的成员和概念，下面的图是一个很好的总结。[[参考1]](#参考)
![Notebook image name](/assets/20190506/dotnetlandscape.png "The .NET Landscape" )

简单来说，.NET Standard 是一套标准，.NET Framework 和 .NET Core 是两套具体的实现。其中 .NET Framework 是大家都熟悉的平时在 Windows 上使用 C# 时的默认框架。.NET Core 是微软新推出的一套免费的，跨平台的，开源的框架，主要支持网络和客户端的编程。关于 .NET Core 的更多信息，可以参考微软的官方网站[[参考2]](#参考)。两者的语言语法等都基本是一样的，只是支持和使用的库有所不同。我们首先是在 Windows 上用 .NET Framework 实现了程序，后来为了支持 Linux ，迁移到 .NET Core 上。

## Windows Docker 和 Linux Docker
Windows Docker 指的是 Windows Server Core 镜像, 是 Windows 操作系统的一个简版。必须运行在部署在 Windows 机器上的 Docker CE 中. Linux docker 这里指的是基于 Linux 操作系统内核构建的 docker 镜像。Linux docker 既可以运行在 Windows 机器的 Docker CE 上，也可以运行在 Linux 机器的 Docker CE上。

### Windows docker + .NET Framework
在 Windows Docker 上运行编译出来的 exe 程序最大的问题是 Windows server core 删减了一些 UI 相关的功能，导致 OpenCV 依赖的一些 dll 找不到。现象就是在本地运行没有问题，在 Docker 中就启动不了。解决方法就是找到系统中这些丢失的系统 dll，拷贝到 docker 中的系统目录下，可以参考以下的 Dockerfile .
``` dockerfile
FROM microsoft/dotnet-framework:4.7.1-windowsservercore-1709
WORKDIR /app
COPY ./DemoModule/bin/Debug ./
COPY ./DemoModule/dlls/ ./

WORKDIR /

COPY ./DemoModule/x64/avifil32.dll ./Windows/System32/avifil32.dll
COPY ./DemoModule/x64/msacm32.dll ./Windows/System32/msacm32.dll
COPY ./DemoModule/x64/avicap32.dll ./Windows/System32/avicap32.dll
COPY ./DemoModule/x64/msvfw32.dll ./Windows/System32/msvfw32.dll
COPY ./DemoModule/x64/opengl32.dll ./Windows/System32/opengl32.dll
COPY ./DemoModule/x64/mfplat.dll ./Windows/System32/mfplat.dll
COPY ./DemoModule/x64/mf.dll ./Windows/System32/mf.dll
COPY ./DemoModule/x64/mfreadwrite.dll ./Windows/System32/mfreadwrite.dll
COPY ./DemoModule/x64/mfcore.dll ./Windows/System32/mfcore.dll
COPY ./DemoModule/x64/mfperfhelper.dll ./Windows/System32/mfperfhelper.dll
COPY ./DemoModule/x64/glu32.dll ./Windows/System32/glu32.dll
COPY ./DemoModule/x64/ddraw.dll ./Windows/System32/ddraw.dll
COPY ./DemoModule/x64/ksuser.dll ./Windows/System32/ksuser.dll

WORKDIR /app
ENTRYPOINT ["DemoModule.exe"]
```
使用 Windows docker 还有以下几个注意点：
1. Windows Server Core 的镜像很大，大概是 6G ，下载需要很长时间。
2. Windows Server Core 启动后默认是占用了 1G 的内存，如果需要更多的内存，可以在启动 Docker 时用参数进行设置，比如 `--memory 2g` , 或者用 `"HostConfig":{ "Memory": 2000000000 }` 。


### Linux docker + .NET Core
从 .NET Framework 迁移到 .NET Core 最大的问题是它们用的库的不同。幸好有 OpenCVSharp 项目[[参考3]](#参考), 这个项目对 OpenCV 进行了包装，支持 .NET Core 2.0, 使用上需要先编译 OpenCV, 再编译 OpenCVSharp ，然后才能在 .NET Core 的项目中使用这个 dll 。可以参考以下这个 Dockerfile 。 为了进一步减少生成的镜像的大小，使用了 multi-stage builds 。

``` dockerfile
# build builder image first
FROM microsoft/dotnet:2.1-runtime as builder
# install dependencies
RUN apt-get update
RUN apt-get install -y git unzip wget cmake libgtk2.0-dev libv4l-dev libavcodec-dev libavformat-dev libswscale-dev

# download opencv
RUN mkdir -p /opencv
RUN wget https://github.com/opencv/opencv/archive/3.4.1.zip
RUN unzip 3.4.1.zip -d /opencv/
RUN rm 3.4.1.zip
RUN wget https://github.com/opencv/opencv_contrib/archive/3.4.1.zip
RUN unzip 3.4.1.zip -d /opencv/
RUN rm 3.4.1.zip

RUN mkdir -p /opencv/build

WORKDIR /opencv/build

# install
RUN cmake -DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib-3.4.1/modules -D WITH_LIBV4L=ON -D CMAKE_BUILD_TYPE=RELEASE -D WITH_TBB=ON -D ENABLE_NEON=ON ../opencv-3.4.1
RUN make -j 5
RUN make install
RUN ldconfig


# get opencvsharp
WORKDIR /
RUN wget https://github.com/shimat/opencvsharp/archive/3.3.1.20171117.zip
RUN unzip 3.3.1.20171117.zip -d /opencvsharp/

WORKDIR /opencvsharp/opencvsharp-3.3.1.20171117/src
RUN cmake .
RUN make -j 5
RUN make install


# build work image (multi-stage builds)
FROM microsoft/dotnet:2.1-runtime
ARG EXE_DIR=.
RUN mkdir /app

RUN apt-get update -y && \
    apt-get install -y libgtk2.0 libv4l-0 libavcodec57 libavformat57 libswscale4 && \
    rm -rf /var/lib/apt/lists/*


COPY --from=builder /usr/local /usr/local
COPY --from=builder /opencvsharp/opencvsharp-3.3.1.20171117/src/OpenCvSharpExtern/libOpenCvSharpExtern.so /app
RUN ldconfig

COPY $EXE_DIR/ /app

WORKDIR /app
CMD ["dotnet", "linuxhatdemo.dll"]
```



## 参考
1. [Demystifying .NET Core and .NET Standard](https://msdn.microsoft.com/en-us/magazine/mt842506.aspx)
2. [.NET](https://dotnet.microsoft.com/)
3. [opencvsharp](https://github.com/shimat/opencvsharp)



