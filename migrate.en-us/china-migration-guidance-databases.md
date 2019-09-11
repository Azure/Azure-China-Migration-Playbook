---
title: Migrate Database Resources
description: This article provides the guidance and tools on migrating databases resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/09/2019
ms.author: frankch

---


# Migrate Database Resources

This section contains information that you may find helpful as you migrate Azure database resources across Azure regions.

## SQL Database

To migrate Azure SQL Database workloads, use Geo-Replication. For detailed instructions, see the blog post [Migrating Azure services to new regions](https://azure.microsoft.com/en-cnuslog/migrating-azure-services-to-new-regions/).

>*NOTE*  
>*The connection string changes after the export operation because the DNS name of the server changes during export.。*

For more information: 
* Learn how to [export a database to a BACPAC file](https://docs.azure.cn/en-us/sql-database/sql-database-export).
* Learn how to [import a BACPAC file to a database](https://docs.azure.cn/en-us/sql-database/sql-database-import).
* Review the [Azure SQL Database documentation](https://docs.azure.cn/en-us/sql-database/).

## Azure Database for MySQL and PostgreSQL

You can restore a server to another Azure region where the service is available if you have configured your server for geo-redundant backups. Geo-restore is the default recovery option when your server is unavailable because of an incident in the region where the server is hosted. If a large-scale incident in a region results in unavailability of your database application, you can restore a server from the geo-redundant backups to a server in any other region. There is a delay between when a backup is taken and when it is replicated to different region. This delay can be up to an hour, so, if a disaster occurs, there can be up to one-hour data loss.。

During geo-restore, the server configurations that can be changed include compute generation, vCore, backup retention period, and backup redundancy options. Changing pricing tier (Basic, General Purpose, or Memory Optimized) or storage size is not supported.

For more information:
* Learn how to [backup and restore Azure Database for MySQL](https://docs.azure.cn/en-us/mysql/concepts-backup).
* Learn how to [backup and restore Azure Database for PostgreSQL](https://docs.azure.cn/en-us/postgresql/concepts-backup).

## SQL Server Stretch Database

Migrating SQL Server Stretch Database across Azure regions isn’t supported at this time.

## SQL Data Warehouse

Migrating SQL Data Warehouse across Azure regions isn’t supported at this time.

## Azure Cache for Redis

There are a few options if you want to migrate an Azure Cache for Redis instance across Azure regions. The option you choose depends on your requirements.

**Option 1: Accept data loss, create a new instance**

This approach makes the most sense when both of the following conditions are true:
*	You’re using Azure Cache for Redis as a transient data cache.
* Your application will repopulate the cache data automatically in the new region. 

To migrate with data loss and create a new instance: 
1.	Create a new Azure Cache for Redis instance in the new target region.
2.	Update your application to use the new instance in the new region.
3.	Delete the old Azure Cache for Redis instance in the source region.


**Option 2: Copy data from the source instance to the target instance**

A member of the Azure Cache for Redis team wrote an open-source tool that copies data from one Azure Cache for Redis instance to another without requiring import or export functionality. See step 4 in the following steps for information about the tool.

To copy data from the source instance to the target instance: 
1. Create a VM in the source region. If your dataset in Azure Cache for Redis is large, make sure that you select a relatively powerful VM size to minimize copying time.
2. Create a new Azure Cache for Redis instance in the target region.
3. Flush data from the **target** instance. (Make sure not to flush from the **source** instance. Flushing is required because the copy tool doesn’t overwrite existing keys in the target location.) 
4. Use the following tool to automatically copy data from the source Azure Cache for Redis instance to the target Azure Cache for Redis instance: [Tool source](https://github.com/deepakverma/redis-copy) and [tool download](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip).

>*NOTE*  
>*This process can take a long time depending on the size of your dataset.*

**Option 3: Export from the source instance, import to the destination instance**

This approach takes advantage of features that are available only in the Premium tier. 
To export from the source instance and import to the destination instance: 
1. Create a new Premium tier Azure Cache for Redis instance in the target region. Use the same size as the source Azure Cache for Redis instance.
2. [Export data from the source cache](https://docs.azure.cn/azure-cache-for-redis/cache-how-to-import-export-data) or use the [Export-AzureRmRedisCache PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azurerm.rediscache/export-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0).
>*NOTE*  
>*The export Azure Storage account must be in the same region as the cache instance. *
3. Copy the exported blobs to a storage account in the destination region by using a tool like AzCopy.
4. [Import data to the destination cache](https://docs.azure.cn/azure-cache-for-redis/cache-how-to-import-export-data) or use the [Import-AzureRmRedisCache PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azurerm.rediscache/import-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0).
5. Reconfigure your application to use the target Azure Cache for Redis instance.

**Option 4: Write data to two Azure Cache for Redis instances, read from one instance**

For this approach, you must modify your application. The application needs to write data to more than one cache instance while reading from one of the cache instances. This approach makes sense if the data stored in Azure Cache for Redis meets the following criteria: ：
* The data is refreshed on a regular basis.
* All data is written to the target Azure Cache for Redis instance
* You have enough time for all data to be refreshed.
 
For more information: 
* Review the [overview of Azure Cache for Redis](https://docs.azure.cn/zh-cn/azure-cache-for-redis/cache-overview).

## Azure Cosmos DB
(Place holder)

## Azure Database for MariaDB
(Place holder)

## Data Factory
(Place holder)

## Azure Database Migration Service
(Place holder)




