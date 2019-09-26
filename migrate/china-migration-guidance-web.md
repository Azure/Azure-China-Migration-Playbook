---
title: 迁移 Web 资源
description: This article provides the guidance and tools on migrating Web resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/16/2019
ms.author: frankch

---

# 迁移 Web 资源

本节包含的信息可帮助您跨 Azure 区域迁移 Azure Web 资源。

## Web 应用

目前不支持跨 Azure 区域迁移使用 Azure App Service 的 Web 应用功能创建的应用。我们建议您将 Web 应用导出为资源管理器模板，备份 Web 应用的文件内容（或确保应用的源代码在源代码控制存储库外部提供），并确保您已脱机存储任何与 Web 应用一同使用的自定义 SSL 证书。然后在将资源管理器模板中的位置属性更改为新区域后重新创建 Web 应用。在新区域中重新创建 Web 应用后，重新发布 Web 应用的文件内容，上传并重新绑定 Web 应用使用的所有自定义 SSL 证书。

>*重要事项:*  
>*更新位置、Azure 密钥保管库密码、证书和其他 GUID 以适配新区域。*

有关更多信息：
* 通过完成[应用服务教程](https://docs.azure.cn/zh-cn/app-service/#step-by-step-tutorials)来刷新您的知识。
* 获取有关如何[导出 Azure 资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)的信息。
* 查阅 [Azure 资源管理器概述](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-overview)。
* 查阅[应用服务概述](https://docs.azure.cn/zh-cn/app-service/overview)。
* 了解如何[重新部署模板](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-template-deploy)。

## API 管理

要将 API 管理端点从一个 Azure 区域迁移到另一个 Azure 区域，可以使用[备份和还原](https://docs.azure.cn/zh-cn/api-management/api-management-howto-disaster-recovery-backup-restore)功能。您应该在源和目标区域中选择相同的 API 管理 SKU。
 
>*注意:*  
>*在不同云类型之间迁移时，备份和还原将不起作用。为此，您需要将资源导出为[模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)。然后，针对目标 Azure 区域调整导出的模板并重新创建资源。*

**选项 1：如果您可以接受使用不同 API 管理实例名称，请遵循这些说明**

1. 使用与源 API 管理实例相同的 SKU 在目标区域使用新名称创建新的 API 管理实例。
2. 将现有 API 管理实例备份到存储帐户。
3. 在目标区域中，将步骤 2 中创建的备份还原到步骤 1 中创建的 API 管理实例。
4. 如果您有一个指向源区域 API 管理实例的自定义域，请更新自定义域 CNAME 以指向新的 API 管理实例。

**选项 2：如果要保留 API 管理实例名称，请按照以下说明操作**

>*注意:*  
>*这是一个有风险的选择，将导致服务中断。*

1. 将源区域中的 API 管理实例备份到一个存储帐户。
2. 删除源区域中的 API 管理实例。
3. 使用与源区域中相同的名称，在目标区域中创建一个新的 API 管理实例。
4. 在目标区域中，将步骤 1 中创建的备份还原为新的 API 管理实例。


## CDN 内容分发网络

CDN 内容分发网络功能没有区域属性，不需要进行迁移。



