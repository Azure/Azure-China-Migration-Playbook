---
title: 迁移 IoT 资源
description: This article provides the guidance and tools on migrating IoT to target Azure region.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/09/2019
ms.author: frankch

---


# 迁移 IoT 资源

本节包含的信息可帮助您跨 Azure 区域迁移 Azure IoT 资源。
 
## Functions

目前不支持跨 Azure 区域迁移 Azure Functions 资源。我们建议您导出资源管理器模板，更改位置，然后重新部署到目标区域。

>*重要事项：*  
>*更改位置、Azure 密钥保管库密码、证书和其他 GUID 以与新区域保持一致。*

有关更多信息：
* 通过完成 [Functions 教程](https://docs.azure.cn/zh-cn/azure-functions/#step-by-step-tutorials)来刷新您的知识。
* 了解如何[导出资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)或阅读 [Azure 资源管理器概述](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-overview)。
* 查阅 [Azure 功能概述](https://docs.azure.cn/zh-cn/azure-functions/functions-overview)。
* 了解如何[重新部署模板](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-template-deploy)。

## 通知中心

要将设置从一个 Azure 通知中心实例迁移到另一个实例，请导出然后导入所有注册令牌和标记：
1. [导出现有通知中心注册令牌](https://docs.azure.cn/zh-cn/notification-hubs/export-modify-registrations-bulk#export)到 Azure Blob 存储容器。
2. 在目标环境中创建新的通知中心。
3. 将[注册令牌](https://docs.azure.cn/zh-cn/notification-hubs/export-modify-registrations-bulk#import)从 Blob 存储导入新的通知中心。

有关更多信息：
* 通过完成[通知中心教程](https://docs.azure.cn/zh-cn/notification-hubs/#step-by-step-tutorials)来刷新您的知识。
* 查阅[通知中心概述](https://docs.azure.cn/zh-cn/notification-hubs/notification-hubs-push-notification-overview)。

## IoT 中心

要迁移 IoT 中心，请重新创建 IoT 中心并使用导出/导入设备标识功能：

>*注意*  
>*此迁移可能会导致 Azure IoT 应用程序出现停机和数据丢失。所有遥测消息、C2D 命令和与作业相关的信息（计划表和历史记录）都不会迁移。您必须重新配置设备和后端应用程序才能开始使用新的连接字符串。*

### 步骤 1：重新创建 IoT 中心
IoT 中心不支持本机克隆。但是，您可以使用 Azure 资源管理器功能将资源组[导出为模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)以导出 IoT 中心元数据。配置的路由和其他 IoT 中心设置包含在导出的元数据中。然后，在全局 Azure 中重新部署模板。通过查看导出的 JSON 中的详细信息，您可以更轻松地在 Azure 门户中重新创建 IoT 中心。

### 步骤 2：迁移设备标识
要迁移设备标识：
1. 在源租户中，使用 [ExportDevices](https://docs.azure.cn/zh-cn/iot-hub/iot-hub-bulk-identity-mgmt)资源管理器将所有设备标识、设备孪生和模块孪生（包括密钥）导出到存储容器。您可以在源 Azure 区域或目标 Azure 区域中使用存储容器。确保生成的共享访问签名 URI 具有足够的权限。
2. 运行 [ImportDevices](https://docs.azure.cn/en-us/iot-hub/iot-hub-bulk-identity-mgmt) 资源管理器 API，将所有设备标识从存储容器导入到目标 Azure 区域中的新 IoT 中心。
3. 重新配置设备和后端服务，以开始使用步骤 1 中创建的新 IoT 中心的新连接字符串。

有关更多信息：
* 了解如何[导出 IoT 中心批处理标识](https://docs.azure.cn/zh-cn/iot-hub/iot-hub-bulk-identity-mgmt#export-devices)。
* 了解如何[导入 IoT 中心批处理标识](https://docs.azure.cn/zh-cn/iot-hub/iot-hub-bulk-identity-mgmt#import-devices)。
* 查阅 [Azure IoT 中心概述](https://docs.azure.cn/zh-cn/iot-hub/about-iot-hub)。


## 事件中心

请查阅[迁移分析服务](./china-migration-guidance-analytics.md)中的**事件中心**章节。

## 流分析

请查阅[迁移分析服务](./china-migration-guidance-analytics.md)中的**流分析**章节。

## 逻辑应用

请查阅[迁移集成资源](./china-migration-guidance-integration.md)中的**逻辑应用**章节。

## API 管理

请查阅[迁移Web资源](./china-migration-guidance-web.md)中的 **API 管理**章节。

 
