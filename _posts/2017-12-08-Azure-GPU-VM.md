---
layout: post
title: "Azure上带GPU的虚拟机的性能"
description: "Azure上带GPU的虚拟机的性能"
category: tech
tags: [Azure, GPU, Nvidia]
modify: 2017-12-08 00:00:00
---

想做深度学习一定会遇到的问题就是 GPU 的性能和选择问题。
在 Azure 上面N系列的虚拟机是带 GPU 的。2016年12月1日正式发布的是 NC 系列的虚拟机。新的 NCv2 和 ND 系列虚拟机即将发布。NCv3 系列在测试阶段，可以在网页上申请测试 [New NVIDIA GPUs coming to Azure accelerate HPC and AI workloads](https://azure.microsoft.com/en-us/blog/new-gpus-coming-to-azure-accelerate-hpc-and-ai-workloads/)。 在 Azure 中国，NCv2 系列的虚拟机也会在不远的将来发布。这里我就简单记录和对比下几块显卡的性能，给大家一个直观性能的参考吧。

| Azure 虚拟机 | GPU 型号 | FLOPS |
|------|---|---|
| NONE | Geforce GTX 1080 Ti | 10T |
| NC   | K80 | 5T |
| ND   | P40 | 10T |
| NCv2 | P100 | 10T |
| NCv3 | V100 | 15T |
| DSv2 | Intel Xeon® E5-2673 v3 (Haswell) | 500G |

注意：
* 上表列的是一个近似值，用于简单估算的。
* 一般可以用 单精度的 FLOPS (Floating-point operations per second,是每秒所执行的浮点运算次数) 来简单评估计算性能。这里没有考虑其它因素的影响，比如显存。显存对深度计算也很重要，影响到能加载的模型大小和 mini-batch 大小等。
* 数据的来源详见参考[4],[5]。同时参考[6]也验证了 P100 和 1080 的性能在一个数量级上。
* **由于 V100 采用新一代的 GPU 架构 (NVIDIA Volta)，实际性能是 P100 的 2.5 倍左右。（详见参考7）**

## 参考
1. [每秒浮点运算次数](https://zh.wikipedia.org/wiki/每秒浮點運算次數)
2. [Azure N-Series: General availability on December 1](https://azure.microsoft.com/en-us/blog/azure-n-series-general-availability-on-december-1/)
3. [New NVIDIA GPUs coming to Azure accelerate HPC and AI workloads](https://azure.microsoft.com/en-us/blog/new-gpus-coming-to-azure-accelerate-hpc-and-ai-workloads/)
4. [Intel Xeon E5-2600 v3 “Haswell” Processor Review](https://www.microway.com/hpc-tech-tips/intel-xeon-e5-2600-v3-haswell-processor-review/)
5. [List of Nvidia graphics processing units](https://en.wikipedia.org/wiki/List_of_Nvidia_graphics_processing_units)
6. [A Comparison between NVIDIA’s GeForce GTX 1080 and Tesla P100 for Deep Learning](https://medium.com/@alexbaldo/a-comparison-between-nvidias-geforce-gtx-1080-and-tesla-p100-for-deep-learning-81a918d5b2c7)
7. [Tesla V100](https://www.nvidia.com/zh-cn/data-center/tesla-v100/)