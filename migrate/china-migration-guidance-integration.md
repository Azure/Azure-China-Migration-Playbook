---
title: 迁移集成资源
description: This article provides the guidance and tools on migrating integration resources to targey Azure region.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/09/2019
ms.author: frankch

---



# 迁移集成资源

本节包含的信息可帮助您跨 Azure 区域迁移 Azure 集成资源。

## 服务总线

Azure 服务总线没有数据导出或导入功能。要跨 Azure 区域迁移服务总线资源，可以将资源导出为[Azure 资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)。然后，针对目标 Azure 区域调整导出的模板并重新创建资源。

>*注意:*  
>*导出资源管理器模板不会复制数据（例如，消息）。导出模板仅重新创建元数据。*

>*重要事项：*  
>*更改位置、Azure 密钥保管库密码、证书和其他 GUID 以与新区域保持一致。*

### 服务总线元数据

导出资源管理器模板时，将重新创建以下服务总线元数据元素：
* 命名空间
* 队列
* 主题
* 订阅
* 规则
* 授权规则

### 密钥

上述导出和重新创建的步骤不会复制与授权规则相关联的共享访问签名密钥。如果需要保留共享访问签名密钥，请使用带有可选参数 -Keyvalue 的 New-AzureRmServiceBuskey cmdlet将密钥作为字符串接受。更新的 cmdlet 可在 [PowerShell Gallery release 6.4.0（2018 年 7 月）](https://www.powershellgallery.com/packages/AzureRM/6.4.0)或 [GitHub](https://github.com/Azure/azure-powershell/releases/tag/v6.4.0-July2018) 上找到。 

### 用法示例:
```PowerShell
New-AzureRmServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string-keyvalue>
```
```powershell
New-AzureRmServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Queue <queuename> -Name <name of Authorization rule> - RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string-keyvalue>
```
```powershell
New-AzureRmServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Topic <topicname> -Name <name of Authorization rule> - RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string-keyvalue>
```
>*注意:*  
>*即使保存了密钥，也必须更新应用程序以使用新的连接字符串。*

有关更多信息：
* 通过完成[服务总线教程](https://docs.azure.cn/zh-cn/service-bus-messaging/#step-by-step-tutorials)来刷新您的知识。
* 熟悉如何[导出资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)，或阅读 [Azure 资源管理器的概述](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-overview)。
* 查阅[服务总线概述](https://docs.azure.cn/zh-cn/service-bus-messaging/service-bus-messaging-overview)。

## 逻辑应用

Azure 计划程序[将于2019年9月30日停用](https://azure.microsoft.com/zh-cn/updates/azure-scheduler-will-retire-on-september-30-2019/)。使用逻辑应用在目标 Azure 区域中创建计划作业。有关从计划程序迁移到逻辑应用的详细步骤，请参阅[将 Azure 计划程序作业迁移到 Azure 逻辑应用](https://docs.azure.cn/zh-cn/scheduler/migrate-from-scheduler-to-logic-apps)。

有关更多信息：
* 通过完成[逻辑应用教程](https://docs.azure.cn/zh-cn/logic-apps/#step-by-step-tutorials)，熟悉 Azure 逻辑应用中的功能。
* 查阅 [Azure 逻辑应用概述](https://docs.azure.cn/zh-cn/logic-apps/logic-apps-overview)。

## API管理

请参阅[迁移Web资源](china-migration-guidance-web.md)中的 **API管理**章节。

## 事件网格
(place holder)
