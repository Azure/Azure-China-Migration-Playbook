---
title: 迁移存储资源
description: This article provides the guidance and tools on migrating storage resouces.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/05/2019
ms.author: frankch

---

# 迁移存储资源

本节包含的信息可以帮助您跨 Azure 区域迁移 Azure 存储资源。

## Blobs

AzCopy 是一个免费工具，可用于复制 blob、文件和表。使用 AzCopy 进行迁移，可以实现跨 Azure 区域的 blob 复制。  
如果不对源 VM 使用托管磁盘，请使用 AzCopy 将 .vhd 文件复制到目标环境。如果使用托管磁盘，请参阅**托管磁盘**。  
以下示例显示了 AzCopy 的使用方法。有关完整参考文献，请参阅 [AzCopy 文档](https://docs.azure.cn/zh-cn/storage/common/storage-use-azcopy-v10)。  
AzCopy 使用 Source 和 Dest 来表示 URI。
 
通过使用 PowerShell 或使用 Azure CLI，您可从门户获得 URI 的三个部分（storageaccountname、containername、blobname）。Blob 的名称可以是 URI 的一部分，或以固定模板提供，如vm121314.vhd。
您还需要使用 Azure Active Directory 或 SAS 令牌进行身份验证才能访问 Azure 存储帐户。有关如何进行身份验证的说明，请参阅[身份验证选项](https://docs.azure.cn/zh-cn/storage/common/storage-use-azcopy-v10#authentication-options)。

例如：  
URI part | example value
-------- | --------------
源 storageAccount | `migratetest`
源 container | `vhds`
源 blob | `vm-121314.vhd`
目标 storageAccount | `migratetarget`
目标 container | `targetcontainer`

此命令可以实现跨 Azure 区域的虚拟硬盘复制：
```cmd
azcopy cp https://migratetest.blob.core.chinacloudapi.cn/vhds/vm-121314.vhd?<sastokenhere> https://migratetarget.blob.core.chinacloudapi.cn /targetcontainer?<sastokenhere>
```
要获得与源 VHD 一致的副本，请为复制操作规划适当的停机时间，并在复制 VHD 之前关闭 VM。复制完成后，[在目标环境中重建 VM](https://docs.azure.cn/zh-cn/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks)。

有关更多信息：
* 查阅 [AzCopy 文档](https://docs.microsoft.com/zh-cn/azure/storage/common/storage-use-azcopy-v10)。
* 了解如何[从还原的磁盘创建 VM](https://docs.azure.cn/zh-cn/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks)。

## 托管磁盘
Azure 托管磁盘通过管理与 VM 磁盘关联的存储帐户，简化了 Azure 基础设施即服务 (IaaS) VM 的磁盘管理。  
由于您无法直接访问 .vhd 文件，因此无法直接使用 AzCopy 等工具来复制文件。解决方法是首先通过获取临时共享访问签名 URI 来导出托管磁盘，然后使用此信息下载或复制它。以下部分介绍了如何获取共享访问签名 URI 以及如何操作的示例：
 
**步骤 1：获取共享访问签名 URI**
1. 在门户中，搜索托管磁盘。它与 VM 位于同一资源组中，其资源类型为**磁盘**。
2. 在**概述**页面上，选择顶部菜单中的**导出**按钮。您必须先关闭并取消分配 VM，或者取消附加 VM 以完成导出。
3. 定义 URI 到期的时间。默认时间为 3600 秒。
4. 生成一个 URL。
5. 复制该 URL。该 URL 只会在创建后显示一次。

**步骤 2：AzCopy**

有关如何使用 AzCopy 的示例，请参阅 **Blobs**。使用 AzCopy 或类似工具将磁盘直接从源环境复制到目标环境。  
在 AzCopy 中，您必须将 URI 拆分为基础 URI 和共享访问签名部分。URI 的共享访问签名部分以字符 "**?**" 开头。共享访问签名 URI 格式类似此 URI：
```http
https://md-kp4qvrzhj4j5.blob.core.chinacloudapi.cn /r0pmw4z3vk1g/abcd?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D>
```
以下命令显示了 AzCopy 的源参数：
```cmd
/source:"https://md-kp4qvrzhj4j5.blob.core.chinacloudapi.cn/r0pmw4z3vk1g/abcd" 
```
```cmd
/sourceSAS:"?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D"
```
以下是完整的命令：
```cmd
azcopy -v /source:"https://md-kp4qvrzhj4j5.blob.core.chinacloudapi.cn /r0pmw4z3vk1g/abcd" /sourceSAS:"?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D" /dest:"https://migratetarget.blob.core.chinacloudapi.cn /targetcontainer/newdisk.vhd" /DestKey:"o//ucD\... Kdpw=="
```
**步骤 3：在目标环境中创建新的托管磁盘**

有几种方法可用于创建新的托管磁盘。以下是在 Azure 门户执行该操作的方法：
1. 在门户中，选择**新建 > 托管磁盘 > 创建**。
2. 输入新磁盘的名称。
3. 选择资源组。
4. 在**源类型**下，选择**存储 blob**。然后，从 AzCopy 命令复制目标 URI，或进行浏览以选择目标 URI。
5. 如果复制的是 OS 磁盘，请选择**OS**类型。对于其他磁盘类型，请选择**创建**。

**步骤 4：创建 VM**

如前所述，有多种方法可以使用此新托管磁盘创建 VM。以下是两种选项：
* 在门户中，选择磁盘，然后选择**创建 VM**。像往常一样定义 VM 的其他参数。
* 利用 PowerShell 的创建，请参阅[从还原磁盘创建 VM](https://docs.azure.cn/zh-cn/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks)。

有关更多信息：
* 了解如何[通过 API](https://docs.microsoft.com/zh-cn/rest/api/compute/disks/grantaccess) 获取共享访问签名 URI，以导出到磁盘。
* 了解如何[通过 API 从非托管 blob 创建托管磁盘](https://docs.microsoft.com/zh-cn/rest/api/compute/disks/createorupdate#create_a_managed_disk_by_importing_an_unmanaged_blob_from_a_different_subscription.)。

## 导入/导出

您无法跨 Azure 区域直接迁移 Azure 导入/导出作业资源。Azure 导入/导出服务不支持资源导出或资源导入。  
在区域中创建的 Azure 导入/导出作业资源需要在该区域中完成作业，以便将数据提取到该区域中的存储帐户或从该区域中的存储帐户导出。  
但是，您可以使用新区域中的存储帐户，在新区域中创建新的 Azure 导入/导出作业资源。  
也可以通过将 Azure 导入/导出资源从源 Azure 区域以[资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)形式导出，然后在目标 Azure 区域使用导出的模板以重新创建作业资源。

>*注意:*  
>*导出 Azure 导入/导出模板不会复制数据（例如，在存储帐户中创建的 Blob）。导出模板仅导出用于重新创建 Azure 导入/导出服务的元数据。*

在 Azure 目标区域导入模板前，请更新交付包、发布信息、存储帐户 ID 和其他属性信息以匹配目标区域。 

### Azure 导入/导出元数据
导出 Azure 导入/导出服务模板时，将重新创建以下元数据元素：
* 作业资源

有关更多信息：
* 查阅 [Azure 导入/导出服务概述](https://docs.azure.cn/zh-cn/storage/common/storage-import-export-service)。
* Azure 导入/导出[常见问题](https://docs.azure.cn/zh-cn/storage/common/storage-import-export-service-faq)。
* 熟悉如何[导出 Azure 资源管理器模板](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)，或阅读 [Azure 资源管理器概述](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-overview)。


## StorSimple

目前不支持将 StorSimple 服务从一个 Azure 区域迁移到另一个 Azure 区域。我们建议您按照[此文档](https://docs.microsoft.com/zh-cn/azure/storsimple/storsimple-8000-migrate-classic-azure-portal#datacenter-changes)描述的手动过程操作或联系客户支持。



