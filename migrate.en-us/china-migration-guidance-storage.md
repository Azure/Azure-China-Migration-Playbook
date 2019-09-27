---
title: Migrate storage resources
description: This article provides the guidance and tools on migrating storage resouces.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/23/2019
ms.author: frankch

---

# Migrate Storage Resources

This section contains information that you may find helpful as you migrate Azure storage resources across Azure regions.

## Blobs

AzCopy is a free tool you can use to copy blobs, files, and tables. Use AzCopy for your migration to copy blobs across Azure regions.
If you don’t use managed disks for your source VM, use AzCopy to copy the .vhd files to
the target environment. If you use managed disks, see **Managed Disks**.
The following example shows how AzCopy works. For a complete reference, see the [AzCopy documentation.](https://docs.azure.cn/storage/common/storage-use-azcopy-v10)
AzCopy uses the terms Source and Dest expressed as URIs.
 
You get the three parts of the URI (storageaccountname, containername, blobname) from the portal, by using PowerShell, or by using the Azure CLI. The name of the blob can be part of the URI or it can be given as a pattern, like vm121314.vhd.

You also need to authenticate with the Azure Active Directory or SAS tokens to access the Azure Storage account. For instructions on how to authenticate, see [Authentication options.](https://docs.azure.cn/storage/common/storage-use-azcopy-v10#authentication-options)

Example:  
URI part | example value
-------- | --------------
Source storageAccount | `migratetest`
Source container | `vhds`
Source blob | `vm-121314.vhd`
Target storageAccount | `migratetarget`
Target container | `targetcontainer`

This command copies a virtual hard disk across Azure regions:
```cmd
azcopy cp https://migratetest.blob.core.chinacloudapi.cn/vhds/vm-121314.vhd?<sastokenhere> https://migratetarget.blob.core.chinacloudapi.cn/targetcontainer?<sastokenhere>
```
To get a consistent copy of the VHD, shut down the VM before you copy the VHD. Plan some downtime for the copy activity. When the VHD is copied, [rebuild your VM in the target environment.](https://docs.azure.cn/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks)。

For more information: 
* 	Review the [AzCopy documentation.](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* 	Learn how to [create a VM from restored disks.](https://docs.azure.cn/en-us/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks)

## Managed Disks
Azure Managed Disks simplifies disk management for Azure infrastructure as a service (IaaS) VMs by managing the storage accounts that are associated with the VM disk.

Because you don’t have direct access to the .vhd file, you can’t directly use tools like AzCopy to copy your files. The workaround is to first export the managed disk by getting a temporary shared access signature URI, and then download it or copy it by using this information. The following sections show an example of how to get the shared access signature URI and what to do with it:
 
**Step 1: Get the shared access signature URI**
1. In the portal, search for your managed disk. It’s in the same resource group as your VM and has a resource type of **Disk**.
2.	On the **Overview** page, select the **Export** button in the top menu. You must shut down and deallocate your VM first, or unattach the VM to complete the export.
3.	Define a time for the URI to expire. The default time is 3,600 seconds.
4.	Generate a URL.
5.	Copy the URL. The url will only be showed one time after creation.

**Step 2：AzCopy**

For examples of how to use AzCopy, see blobs. Use AzCopy or a similar tool to copy the disk directly from your source environment to the target environment. In AzCopy, you must split the URI into the base URI and the shared access signature part. The shared access signature part of the URI begins with the character “**?**”. The portal provides this URI for the shared access signature URI:

```http
https://md-kp4qvrzhj4j5.blob.core.chinacloudapi.cn/r0pmw4z3vk1g/abcd?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D>
```
The following commands show the source parameters for AzCopy:
```cmd
/source:"https://md-kp4qvrzhj4j5.blob.core.chinacloudapi.cn/r0pmw4z3vk1g/abcd" 
```
```cmd
/sourceSAS:"?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D"
```
Here’s the complete command:
```cmd
azcopy -v /source:"https://md-kp4qvrzhj4j5.blob.core.windows.net/r0pmw4z3vk1g/abcd" /sourceSAS:"?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D" /dest:"https://migratetarget.blob.core.chinacloudapi.cn/targetcontainer/newdisk.vhd" /DestKey:"o//ucD\... Kdpw=="
```
**Step 3: Create a new managed disk in the target environment**

There are several options for creating a new managed disk. Here’s how to do it in the Azure portal: 

1.	In the portal, select **New > Managed Disk > Create**. 
2.	Enter a name for the new disk. 
3.	Select a resource group.
4.	Under **Source type**, select **Storage blob**. Then, either copy the destination URI from the AzCopy command or browse to select the destination URI.
5.	If you copied an **OS** disk, select the OS type. For other disk types, select **Create**.   

**Step 4: Create the VM**

As noted earlier, there are multiple ways to create a VM by using this new managed disk. Here are two options: 
* In the portal, select the disk, and then select **Create VM**. Define the other parameters of your VM as usual.
* For PowerShell, see [Create a VM from restored disks.](https://docs.azure.cn/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks)

For more information: 
* Learn how to export to disk [via API](https://docs.microsoft.com/rest/api/compute/disks/grantaccess) by getting a shared access signature URI. 
* Learn how to create a managed disk [via API](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#create_a_managed_disk_by_importing_an_unmanaged_blob_from_a_different_subscription.) from an unmanaged blob.

## Import/Export

You can’t directly migrate Azure Import/Export job resources across Azure regions. The Azure Import/Export service doesn’t support resource export or resource import.
An Azure Import/Export job resource created in a region needs to finish in the region so that the data is ingested to or exported from the storage account in that region. 
However, you can create a new Azure Import/Export job resource in the new region, with a storage account in the new region.
Creating a new job resource can also be done by exporting Azure Import/Export resources [as a Resource Manager template](https://docs.azure.cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates), and then adapting the exported template for the target Azure region to re-create the resources.

>*NOTE*  
>*Exporting an Azure Import/Export template doesn’t copy data (for example, blobs created in storage account). Exporting a template only re-creates Azure Import/Export metadata.*

Consider changing the delivery Package, shipping Information, storage account Id, and other job properties as appropriate for the new region.

### Azure Import/Export metadata
The following metadata elements are re-created when you export an Azure Import/Export template: 
* Job Resource

For more information: 
* Review the [Azure Import/Export Service overview.](https://docs.azure.cn/storage/common/storage-import-export-service)
* Azure Import/Export [Frequently Asked Questions.](https://docs.azure.cn/storage/common/storage-import-export-service-faq)
* Become familiar with how to [export Azure Resource Manager templates](https://docs.azure.cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates) or read the overview of [Azure Resource Manager](https://docs.azure.cn/azure-resource-manager/resource-group-overview).


## StorSimple

Migrating StorSimple service from one Azure region to another isn’t supported at this time. We recommend you follow the manual process described [here](https://docs.microsoft.com/azure/storsimple/storsimple-8000-migrate-classic-azure-portal#datacenter-changes) or contact customer support.




