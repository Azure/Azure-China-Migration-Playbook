---
title: 迁移数据库资源
description: This article provides the guidance and tools on migrating databases resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/06/2019
ms.author: frankch

---


# 迁移数据库资源

本节包含的信息可帮助您跨 Azure 区域迁移 Azure 数据库资源。

## SQL 数据库

要迁移 Azure SQL 数据库工作负载，请使用异地复制。有关详细说明，请参阅博客文章[将 Azure 服务迁移到新区域](https://azure.microsoft.com/zh-cn/blog/migrating-azure-services-to-new-regions/)。

>*注意:*  
>*导出操作后连接字符串会更改，因为导出期间服务器的 DNS 名称会更改。*

有关更多信息：
* 了解如何[将数据库导出到 BACPAC 文件](https://docs.azure.cn/zh-cn/sql-database/sql-database-export)。
* 了解如何[将 BACPAC 文件导入数据库](https://docs.azure.cn/zh-cn/sql-database/sql-database-import)。
* 查阅 [Azure SQL 数据库文档](https://docs.azure.cn/zh-cn/sql-database/)。

## Azure Database for MySQL and PostgreSQL

如果您的服务器已配置地域冗余备份，则可以还原到任意其他支持该功能的 Azure 区域。当您的服务器因托管服务器的故障而不可用时，地域还原是默认的恢复选项。如果某个区域中的大规模事件导致数据库应用程序不可用，则可以将服务器从地域冗余备份还原到任何其他区域中的服务器。在进行备份和跨区域还原期间，存在一定的延迟，此延迟可能长达一个小时。因此，如果发生灾难，可能会丢失最多一小时的数据。  
在跨地域还原过程中，支持更改的服务器配置包括计算资源代系、vCore数量、备份保留期和备份冗余选项。不支持更改价格阶梯（基础版、通用版或内存优化版）或存储大小。

有关更多信息：
* 了解如何[备份和还原 Azure Database for MySQL](https://docs.azure.cn/zh-cn/mysql/concepts-backup)
* 了解如何[备份和还原 Azure Database for PostgreSQL](https://docs.azure.cn/zh-cn/postgresql/concepts-backup)

## SQL Server Stretch Database

目前不支持跨 Azure 区域迁移 SQL Server Stretch Database 。

## SQL 数据仓库

目前不支持跨 Azure 区域迁移 SQL 数据仓库。

## Azure Cache for Redis

如果要跨 Azure 区域迁移 Azure Cache for Redis 实例，可以有几个选项。您可以根据自身需要进行选择。

**选项 1：接受数据丢失，创建一个新实例**

当满足以下两个条件时，此方法最合理：
* 您正在使用 Azure Cache for Redis 作为临时数据缓存。
* 您的应用程序将在新区域中自动重新填充缓存数据。

要在数据丢失的情况下迁移并创建新实例：
1. 在新目标区域中创建新的 Azure Cache for Redis 实例。
2. 更新您的应用程序以使用新区域中的新实例。
3. 删除源区域中的旧 Azure Cache for Redis 实例。

**选项 2：将数据从源实例复制到目标实例**

Azure Cache for Redis 团队的一名成员编写了一个开源工具，可以将数据从一个 Azure Cache for Redis 实例复制到另一个实例，而无需使用导入或导出功能。有关该工具的信息，请参阅以下步骤中的步骤 4。

要将数据从源实例复制到目标实例：
1. 在源区域中创建 VM。如果 Azure Cache for Redis 中的数据集很大，请确保选择相对较大的 VM 大小以尽量减少复制时间。
2. 在新目标区域中创建新的 Azure Cache for Redis。
3. 从**目标**实例刷新数据。（确保不要从**源**实例刷新。由于复制工具不会覆盖目标位置中的现有密钥，因此需要刷新。）
4. 使用以下工具自动将源 Azure Cache for Redis 实例中的数据复制到目标 Azure Cache for Redis 实例：[工具源代码](https://github.com/deepakverma/redis-copy)和[工具下载](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip)。

>*注意:*  
>*此过程可能需要很长时间，具体取决于数据集的大小。*

**选项 3：从源实例导出并导入到目标实例**

此方法利用仅在 Premium 价格段中提供的功能。要从源实例导出并导入到目标实例：
1. 在目标区域中创建新的 Premium 价格段的 Azure Cache for Redis 实例。实例的大小需要与源 Azure Cache for Redis 实例相同。
2. [从源缓存中导出数据](https://docs.azure.cn/zh-cn/azure-cache-for-redis/cache-how-to-import-export-data)或使用PowerShell cmdlet [Export-AzureRmRedisCache](https://docs.microsoft.com/zh-cn/powershell/module/azurerm.rediscache/export-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0)完成导出。

>*注意:*  
>*导出 Azure 存储帐户必须与缓存实例位于同一区域。*

3. 使用 AzCopy 之类的工具将导出的 blob 复制到目标区域中的存储帐户。
4. [将数据导入目标缓存](https://docs.azure.cn/zh-cn/azure-cache-for-redis/cache-how-to-import-export-data)或使用 PowerShell cmdlet [Import-AzureRmRedisCache](https://docs.microsoft.com/zh-cn/powershell/module/azurerm.rediscache/import-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0) 完成导入。
5. 重新配置应用程序以使用目标 Azure Cache for Redis 实例。

**选项 4：将数据写入两个 Azure Cache for Redis 实例，并从一个实例中读取**

对于此方法，您必须修改应用程序。从其中一个缓存实例读取数据时，应用程序需要将数据写入多个缓存实例。如果存储在 Azure Cache for Redis 中的数据满足以下条件，则可采用此方法：
* 数据会定期刷新。
* 所有数据都将写入目标 Azure Cache for Redis 实例。
* 您有足够的时间刷新所有数据。
 
有关更多信息：
* 查阅 [Azure Cache for Redis 概述](https://docs.azure.cn/zh-cn/azure-cache-for-redis/cache-overview)。

