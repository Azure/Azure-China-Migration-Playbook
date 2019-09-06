---
title: 计算资源跨迁移
description: This article provides the guidance and tools on migrating compute resourse(tbd
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/06/2019
ms.author: frankch

---

# 计算资源迁移
本节提供的信息可帮助您将已部署Azure计算资源从一个Azure区域迁移到其他Azure区域。
  
## 虚拟机  
由于目前中国所有Azure区域都处于[Azure 站点恢复（ASR）](https://docs.azure.cn/zh-cn/site-recovery/site-recovery-overview)所支持的相同地理集群（关于地理群集，[请参阅](https://docs.azure.cn/zh-cn/site-recovery/azure-to-azure-support-matrix#region-support))。可以参照[迁移Azure虚拟机到另一个区域](https://docs.azure.cn/zh-cn/site-recovery/azure-to-azure-tutorial-migrate)所描述的步骤完成虚拟机的迁移。
  
## 云服务  
目前还不支持将云服务从一个 Azure 区迁移到另一个区域。您可以利用 .cspkg 和 .cscfg 定义来重新部署 Azure 云服务资源到另一个区域。
  
### 利用Azure 门户  
要在 Azure 门户中重新部署云服务，请执行以下操作：
1. 使用 .cspkg 和 .cscfg 定义[创建新的云服务](https://docs.azure.cn/zh-cn/cloud-services/cloud-services-how-to-create-deploy-portal)。
2. 更新[CNAME 或 A 记录](https://docs.azure.cn/zh-cn/cloud-services/cloud-services-custom-domain-name-portal)以将流量导向新的云服务。
3. 当流量导向新的云服务后，删除源区域中的旧云服务。
  
### 利用PowerShell  
要使用 PowerShell 重新部署云服务，请执行以下操作：
1. 使用 .cspkg 和 .cscfg 定义[创建新的云服务](https://docs.microsoft.com/zh-cn/powershell/module/servicemanagement/azure/new-azureservice)。
```PowerShell
New-AzureService -ServiceName <yourServiceName> -Label <MyTestService> -Location <targetRegion>  
```
2. 使用 .cspkg 和 .cscfg 定义[创建新的部署](https://docs.microsoft.com/zh-cn/powershell/module/servicemanagement/azure/new-azuredeployment)。  
```PowerShell
New-AzureDeployment -ServiceName <yourServiceName> -Slot <Production> -Package <YourCspkgFile.cspkg> -Configuration <YourConfigFile.cscfg>  
```
3. 更新[CNAME 或 A 记录](https://docs.azure.cn/zh-cn/cloud-services/cloud-services-custom-domain-name-portal)以将访问导向新的云服务。
4. 当访问导向新的云服务后，[删除源 Azure 区域中的旧云服务](https://docs.microsoft.com/zh-cn/powershell/module/servicemanagement/azure/remove-azureservice)。  
```PowerShell
Remove-AzureService -ServiceName <yourOldServiceName>
```
  
### 利用REST API  
要使用 REST API 重新部署云服务，请执行以下操作：
1. 在目标环境中[创建新的云服务](https://docs.microsoft.com/zh-cn/rest/api/compute/cloudservices/rest-create-cloud-service)。
```http
https://management.core.windows.net/<subscription-id>/services/hostedservices  
```
2. 使用[创建部署 API](https://msdn.microsoft.com/library/azure/ee460813.aspx)创建新的部署。要获取您的 .cspkg 和 .cscfg 定义，可以调用[Get Package API](https://docs.microsoft.com/en-us/previous-versions/azure/reference/jj154121(v=azure.100))。 
```http
https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deploymentslots/production  
```
3. 当流量指向新的云服务时，[删除源 Azure 区域中的旧云服务](https://docs.microsoft.com/zh-cn/rest/api/compute/cloudservices/rest-delete-cloud-service)。  
```http
https://management.core.windows.net/<subscription-id>/services/hostedservices/<old-cloudservice-name>
```
  
更多相关信息：
* 请参考[Azure云服务概述](https://docs.azure.cn/zh-cn/cloud-services/cloud-services-choose-me)。
  
## Service Fabric  
要将 Azure Service Fabric 资源从一个 Azure 区域迁移到另一个 Azure 区域，您需要在新区域中创建和重新部署 Service Fabric 群集和应用程序资源。必须备份旧群集中的数据，并将其还原到新群集以保证业务的运行状态。
1. 阅读[生产准备清单](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-production-readiness-checklist)中推荐的指南，使用[Azure 门户](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-cluster-creation-via-portal)或资源管理器资源在目标 Azure 区域的资源组中创建新群集。
2. 不应该出现新的调用，也不应该有任何服务相互通信或执行工作。
3. 如果它是**有状态服务**，则需要将数据从旧群集移动到新群集。请参阅[从旧群集备份数据](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)，以了解如何将数据从旧群集移动到新群集的信息。
4. 如果它是**无状态服务**，则不需要移动数据，但您必须重新配置流量。
5. 更新要部署到新区域的应用程序配置。
6. [使用资源管理器资源将应用程序部署到新区域](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-application-arm-resource)。
7. 对于**有状态服务**，您需要使用上述步骤 #3 中的备份[还原数据](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-reliable-services-backup-restore#restore-reliable-services)。
8. 更新流量管理器服务（**Azure 流量管理器**）以将流量路由到目标区域。
9. 验证应用程序是否已部署到目标 Azure 区域并接受流量。
10. 验证没有流量流向源 Azure 区域。 
11. [通过清除与源 Azure 区域中的群集相关联的资源组删除群集](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-tutorial-delete-cluster#delete-the-resource-group-containing-the-service-fabric-cluster)。

参考文献：
* [创建 Azure Windows Service Fabric 群集](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-quickstart-containers)
* [创建 Linux Azure Linux Service Fabric 群集](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-quickstart-containers-linux)
* [使用 PowerShell 部署应用程序](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-deploy-remove-applications)

有关更多信息：
* 通过完成[Service Fabric教程](https://docs.azure.cn/zh-cn/service-fabric/#step-by-step-tutorials)来刷新您的知识。
* 了解如何[创建新群集](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-cluster-creation-via-portal)。
* 查阅[Service Fabric概述](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-overview)。

## 批处理

您无法自动将 Azure 批处理帐户和数据从一个区域迁移到另一个区域。如需迁移，您需要完成以下步骤：
1. 在目标区域中[创建批处理帐户](https://docs.azure.cn/zh-cn/batch/batch-account-create-portal)。确保目标区域中有存储帐户，或在目标区域创建存储帐户。
2. 将工作负载部署到新的批处理帐户并开始在那里运行作业。

有关更多信息：
* 通过完成[批处理教程](https://docs.azure.cn/zh-cn/batch/#step-by-step-tutorials)来刷新您的知识。
* 查阅[批处理概述](https://docs.azure.cn/zh-cn/batch/batch-technical-overview)。

## 虚拟机规模集

要跨 Azure 区域迁移虚拟机规模集，请导出资源管理器模板，根据新环境对其进行调整，然后重新部署到目标区域。只需导出基本模板并在新环境中重新部署模板。单个虚拟机规模集实例应该都相同。在开始重新部署之前，请确保了解其他资源的依赖关系并将其迁移到目标区域。

>*重要事项*:  
>*更改位置、密钥保管库密码、证书和其他 GUID 以与新区域保持一致。*
 
有关更多信息：
* 通过完成[虚拟机规模集教程](https://docs.azure.cn/zh-cn/virtual-machine-scale-sets/#step-by-step-tutorials)来刷新您的知识。
* 了解如何[导出 Azure 资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)。
* 查阅[Azure资源管理器概述](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-overview)。
* 获取[虚拟机规模集概述](https://docs.azure.cn/zh-cn/virtual-machine-scale-sets/overview)。
* 阅读[Azure区域概述](https://www.azure.cn/zh-cn/home/features/products-by-region)。
* 了解如何[重新部署模板](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-template-deploy)。
