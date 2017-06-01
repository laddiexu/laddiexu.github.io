---
layout: post
title: "Azure ARM Template 部署虚拟机失败的原因查找"
description: ""
category: work
tags: [Azure]
modify: 2017-05-24 21:37:00
---

如果你想用ARM Template来部署和创建虚拟机，但是发现同一份脚本Global Azure部署没有问题，在中国的由21V运营的Azure上部署有问题，有下面几个可能的点可以查看下：
1. 检查当前的powershell 版本是否过低  
    使用下面的命令可以查看当前安装的powershell的版本号：
     ````powershell
    Get-Module | Where Name -like '*Azure*'
    ````
    可以从下面这个网站下载和更新 powershell :
    [powershell release versions](https://github.com/Azure/azure-powershell/releases)


2. 检查每个用到的 ResouceProvider 的 API version  
    使用下面这个powershell的命令查看当前中国Azure支持的ResourceProvider的API version：

    ````powershell
    Login-AzureRmAccount -EnvironmentName AzureChinaCloud；
    (Get-AzureRmResourceProvider).ResourceTypes | Out-GridView
    ````


3. 如果你是从Global Azure 创建的虚拟机中复制脚本来中国Azure 部署的话，很有可能是由于中国区的镜像市场和global的Marketplace不一样，以下的几个关于镜像的参数 ***plan*** , ***imageReference*** 都不能设置：

    ```` json
    "plan": {
        "name": "6_8",
        "publisher": "cloudera",
        "product": "cloudera-centos-os"
        },

    "imageReference": {
        "publisher": "cloudera",
        "offer": "cloudera-centos-os",
        "sku": "6_8",   
        "version": "latest"
        }
    ````


4. 如果是从一个自己的VHD创建虚拟机，需要把VHD和最后创建的虚拟机的磁盘在一个存储账号里（storage account)。

### 最后附上一份template.json 供参考： 实现从自己的VHD创建一个带一个数据盘的虚拟：

````json
{
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[parameters('virtualMachineName')]",
    "apiVersion": "2016-08-30",
    "location": "[parameters('location')]",
    "properties": {
        "osProfile": {
            "computerName": "[parameters('virtualMachineName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
        },
        "hardwareProfile": {
            "vmSize": "[parameters('virtualMachineSize')]"
        },
        "storageProfile": {
            "osDisk": {
                "createOption": "FromImage",
                "vhd": {
                    "uri": "https://{yourstorageaccount}.blob.core.chinacloudapi.cn/vhds/{vmOSDiskName}.vhd"
                },
                "image": {
                    "uri": "https://{yourstorageaccount}.blob.core.chinacloudapi.cn/vhds/{refferenceOSImage}.vhd"
                },
                "name": "[parameters('virtualMachineName')]",
                "osType": "Linux",
                "caching": "ReadWrite"
            },
            "dataDisks": [{
                "createOption": "FromImage",
                "vhd": {
                    "uri": "https://{yourstorageaccount}.blob.core.chinacloudapi.cn/vhds/{vmDataDiskName}.vhd"
                },
                "image": {
                    "uri": "https://{yourstorageaccount].blob.core.chinacloudapi.cn/vhds/{refferenceDataImage}.vhd"
                },
                "name": "[concat(parameters('virtualMachineName'),'-dataDisk')]",
                "lun": 0
            }]
        },
        "networkProfile": {
            "networkInterfaces": [{
                "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
            }]
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
    ]
}
````