---
title: Migrate web resources
description: This article provides the guidance and tools on migrating Web resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/23/2019
ms.author: frankch

---

# Migrate web resources

This section contains information that you may helpful as you migrate Azure web resources across Azure regions.

## Web Apps

Migrating apps that you created by using the Web Apps feature of Azure App Service
across Azure regions isn’t supported at this time. We recommend that you export a web app as a Resource Manager template, back up your web app’s file content (or ensure the application’s source code is available externally in a source code control repository) and ensure you’ve stored offline any custom SSL certificates used with your web apps. Then recreate your web app after you change the location property in your Resource Manager template to the new region. Once the web app has been recreated in the new region, republish your web app’s file content and upload and rebind any custom SSL certificates used by the web app.

>*IMPORTANT*  
>*Change location, Azure Key Vault secrets, certificates, and other GUIDs to be consistent with the new region.*

For more information: 
* Refresh your knowledge by completing the [App Service tutorials.](https://docs.azure.cn/app-service/#step-by-step-tutorials)
* Get information about how to [export Azure Resource Manager templates.](https://docs.azure.cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)
* Review the [Azure Resource Manager overview.](https://docs.azure.cn/azure-resource-manager/resource-group-overview)
* Get an [overview of Azure locations.](https://docs.azure.cn/app-service/overview)
* Learn how to [redeploy a template.](https://docs.azure.cn/azure-resource-manager/resource-group-template-deploy)

## API Management

To migrate API Management endpoints from one Azure region to another, you can use the [backup and restore](https://docs.azure.cn/zh-cn/api-management/api-management-howto-disaster-recovery-backup-restore) feature. You should choose the same API Management SKU in the source and the target region.

>*NOTE*  
>*Backup and restore won’t work while migrating between different cloud types. For that, you’ll need to export the resource [as a template](https://docs.azure.cn/zh-cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates). Then, adapt the exported template for the target Azure region and re- create the resources.*

**Option 1: If you’re okay with a different API Management instance name, then follow
these instructions:**

1.	Create a new API Management instance with the same SKU as the source API Management instance in the target region with a new name.
2.	Backup existing API Management instance to a storage account.
3.	Restore the backup created in Step 2 to API Management instance created in Step 1 in the target region.
4.	If you have a custom domain pointing to the source region API Management instance, update the custom domain CNAME to point to the new API Management instance. 

**Option 2: If you would like to preserve the API Management instance name, then follow the below instructions**

>*NOTE*  
>*This is a riskier option and will result in downtime of the service.*

1.	Back up the API Management instance in the source region to a storage account
2.	Delete the API Management instance in the source region
3.	Create a new API Management instance in the target region with the same name as the one in the source region.
4.	Restore the backup created in Step 1 to the new API Management instance in the target region.


## CDN 

CDN functiona has no regional properties and no migration is required.


## App Service

See **App Service** Chapter of [Migrate Compute Resources.](./china-migration-guidance-compute.md)
(blank)
