---
title: Migrate Management Tool Resources
description: This article provides the guidance and tools on migrating media resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/23/2019
ms.author: frankch

---


# Migrate Management Tool Resources

This section contains information that you may find helpful as you migrate Azure management tool resources across Azure regions.

## Backup

Azure Backup is the Azure-based service used to back up (or protect) and restore data in Azure. The service can protect native Azure resources like VMs as well non-Azure resources from hybrid environments including workloads running on on-premises servers. All backups are stored in Recovery Services Vaults in Azure.
If customers need to migrate between Azure regions, they should first create a new Recovery Services Vault in the target region that can protect resources in the new region. To migrate Azure resources protected by Azure Backup, customers will first need to stop protection on the resources and retain the existing data ([Instructions](https://docs.azure.cn/backup/backup-azure-manage-vms#stop-protecting-a-vm)). Then the resources can then be migrated to the target region and protection can be enabled in the newly created recovery vault. For non-Azure resources, customers would follow the same process but don't need to migrate resources. The existing backup data in the source region will still be accessible to support recovery scenarios.

For customers looking to unify their backups, Azure Backup is working to provide a tool to enable moving existing data to the target region while ensuring existing recovery points are still accessible by customers. The tool is expected to be available in CY20H1.

For more information

* Refresh your knowledge by completing the [Backup tutorials.](https://docs.azure.cn/backup/#step-by-step-tutorials)
* Review the [Azure Backup overview.](https://docs.azure.cn/backup/backup-overview)

## Scheduler

Azure Scheduler will [retire on September 30, 2019.](https://azure.microsoft.com/updates/azure-scheduler-will-retire-on-september-30-2019/) Use Azure Logic Apps to create scheduling jobs. For detailed steps on migrating from Scheduler to Logic Apps, see [Migrate Azure Scheduler jobs to Azure Logic Apps.](https://docs.azure.cn/scheduler/migrate-from-scheduler-to-logic-apps)

For more information: 
* Become familiar with features in Azure Logic Apps by completing the [Logic Apps tutorials.](https://docs.azure.cn/logic-apps/#step-by-step-tutorials)
* Review the [Logic Apps overview.](https://docs.azure.cn/logic-apps/logic-apps-overview)
 
## Site Recovery

You can’t move an existing Azure Site Recovery setup across Azure regions. [Disable](https://docs.azure.cn/site-recovery/site-recovery-manage-registration-and-protection) the existing configuration and set up a new Site Recovery solution in the target Azure region.

Refresh your knowledge by completing these step-by-step tutorials:
* [Azure-to-Azure disaster recovery](https://docs.azure.cn/site-recovery/#azure-to-azure)
* [VMware-to-Azure disaster recovery](https://docs.azure.cn/site-recovery/#vmware)
* [Hyper-V-to-Azure disaster recovery](https://docs.azure.cn/site-recovery/#hyper-v)

## Traffic Manager

See **Traffic Manager** Chapter of [Migrate Network Resources](china-migration-guidance-networking.md).

## Network Watcher

See **Network Watcher** Chapter of [Migrate Network Resources](china-migration-guidance-networking.md).


## Azure Advisor

Azure Advisor feature is non-regional and no migration is required.


## Azure Monitor

Azure Monitor feature is non-regional and no migration is required.


## Azure Policy

Azure policy feature is non-regional and no migration is required.

