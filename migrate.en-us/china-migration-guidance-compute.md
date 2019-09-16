---
title: Migrate Compute Resources
description: This article provides the guidance and tools on migrating compute resourse.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/16/2019
ms.author: frankch

---

# Migrate Compute Resources

This section provides information that you may find helpful as you migrate your Azure compute resources from one Azure region to another.
  
## Virtual Machine

Now all China Azure regions are same geographic cluster supported by [Azure Site Recoverty](https://docs.azure.cn/en-us/site-recovery/site-recovery-overview) (See [geographic cluster](https://docs.azure.cn/en-us/site-recovery/azure-to-azure-support-matrix#region-support)).

See [Move Azure VMs to another region](https://docs.azure.cn/en-us/site-recovery/azure-to-azure-tutorial-migrate)to migrate VMs.
  
## Cloud Services  

Cloud Services can’t be migrated from one Azure region to another. You can redeploy Azure Cloud Services resources by providing the .cspkg and .cscfg definitions again.
  
### Azure portal
To redeploy cloud services in the Azure portal: 
1. [Create a new cloud service](https://docs.azure.cn/en-us/cloud-services/cloud-services-how-to-create-deploy-portal)by using your .cspkg and .cscfg definitions.
2. Update the [CNAME or A record](https://docs.azure.cn/en-us/cloud-services/cloud-services-custom-domain-name-portal) to point traffic to the new cloud service.
3. When traffic points to the new cloud service, delete the old cloud service in the source region.
  
### PowerShell  
To redeploy cloud services by using PowerShell:
1. [Create a new cloud service](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure/new-azureservice) by using your .cspkg and .cscfg definitions.
```PowerShell
New-AzureService -ServiceName <yourServiceName> -Label <MyTestService> -Location <targetRegion>  
```
2. [Create a new deployment](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure/new-azuredeployment) by using your .cspkg and .cscfg definitions.
```PowerShell
New-AzureDeployment -ServiceName <yourServiceName> -Slot <Production> -Package <YourCspkgFile.cspkg> -Configuration <YourConfigFile.cscfg>  
```
3. Update the [CNAME or A record](https://docs.azure.cn/en-us/cloud-services/cloud-services-custom-domain-name-portal) to point traffic to the new cloud service.
4. When traffic points to the new cloud service, [delete the old cloud service](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure/remove-azureservice) in the source Azure region.
```PowerShell
Remove-AzureService -ServiceName <yourOldServiceName>
```
  
### REST API  
To redeploy cloud services by using the REST API:
1. [Create a new cloud service](https://docs.microsoft.com/en-us/rest/api/compute/cloudservices/rest-create-cloud-service) in the target environment.
```http
https://management.core.windows.net/<subscription-id>/services/hostedservices  
```
2. Create a new deployment by using the [Create Deployment API](https://msdn.microsoft.com/library/azure/ee460813.aspx). To find your .cspkg and .cscfg definitions, you can call the [Get Package API].(https://docs.microsoft.com/en-us/previous-versions/azure/reference/jj154121(v=azure.100))
```http
https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deploymentslots/production  
```
3. When traffic points to the new cloud service, [delete the old cloud service in the source Azure region](https://docs.microsoft.com/en-us/rest/api/compute/cloudservices/rest-delete-cloud-service). 
```http
```
For more information: 
*	Review the Cloud Services overview(https://docs.azure.cn/en-us/cloud-services/cloud-services-choose-me).
  
## Service Fabric  

To migrate Azure Service Fabric resources from one Azure region to another, you need to create and redeploy the Service Fabric cluster and application resources in the new region. Data must be backed up and restored from old to new cluster for stateful services. 

1.	Create a new cluster in a resource group in the target Azure region using the [Azure portal](https://docs.azure.cn/en-us/service-fabric/service-fabric-cluster-creation-via-portal) or Resource Manager resources using guidelines recommended in the [Production readiness checklist](https://docs.azure.cn/en-us/service-fabric/service-fabric-production-readiness-checklist).

2.	No new calls should show up and no services should talk to each other or do work.

3.	If it’s a **stateful service**, then there’s a requirement to move data from the old cluster to the new cluster. See, [Backup data from the old cluster](https://docs.azure.cn/en-us/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster) to learn how to move data from the old cluster to the new one.

4.	If it’s a **stateless service**, then there’s no requirement to move data but you must move the traffic.

5.	Update the application configuration to be deployed to a the new region.

6.	[Deploy an application to the new region using Resource Manager resources](https://docs.azure.cn/en-us/service-fabric/service-fabric-application-arm-resource).

7.	For **stateful services**, you’ll need to [restore the data](https://docs.azure.cn/en-us/service-fabric/service-fabric-reliable-services-backup-restore#restore-reliable-services) using a backup taken in step #3 above.

8.	Update the traffic manager service (**Azure Traffic Manager**) to route active traffic to the target region. Gradually move the application traffic to the new region.
 
9.	Verify the application is deployed to the target Azure region and accepting traffic.

10.	Validate there’s no traffic flowing to the source Azure region. 

11.	[Delete the cluster by cleaning resource group associated with the cluster in the source Azure region](https://docs.azure.cn/zh-cn/service-fabric/service-fabric-tutorial-delete-cluster#delete-the-resource-group-containing-the-service-fabric-cluster). 


References: 
* [Create an Azure windows Service Fabric cluster](https://docs.azure.cn/en-us/service-fabric/service-fabric-quickstart-containers)
* [Create Linux Azure Linux Service fabric cluster](https://docs.azure.cn/en-us/service-fabric/service-fabric-quickstart-containers-linux)
* [Deploy application using PowerShell](https://docs.azure.cn/en-us/service-fabric/service-fabric-deploy-remove-applications)

For more information: 
* Refresh your knowledge by completing the [Service Fabric tutorials](https://docs.azure.cn/en-us/service-fabric/#step-by-step-tutorials).
* Learn how to [create a new cluster](https://docs.azure.cn/en-us/service-fabric/service-fabric-cluster-creation-via-portal).
*	Review the [Service Fabric overview](https://docs.azure.cn/en-us/service-fabric/service-fabric-overview).

## Batch

You can’t automatically migrate your Azure Batch account and data from one region to another. Instead, to migrate you’ll need to complete the following steps: 
1. [Create a Batch account](https://docs.azure.cn/en-us/batch/batch-account-create-portal) in the target region. Make sure there are storage accounts in the target region(s) or create them there.
2. Deploy your workloads to the new Batch account and start running the jobs there. 

For more information: 
* Refresh your knowledge by completing the [Batch tutorials](https://docs.azure.cn/en-us/batch/#step-by-step-tutorials).
* •	Review the [Azure Batch overview](https://docs.azure.cn/en-us/batch/batch-technical-overview).

## Virtual machine scale sets

To migrate virtual machine scale sets across Azure regions, export the Resource Manager template, adapt it to the new environment, and then redeploy to the target region. Export only the base template and redeploy the template in the new environment. Individual virtual machine scale set instances should all be the same. Before starting the redeployment, ensure dependencies on other resources are understood and migrated to the target region.

>*IMPORTANT*:  
>*Change location, Key Vault secrets, certificates, and other GUIDs to be consistent with the new region.*
 
For more information:
* Refresh your knowledge by completing the [virtual machine scale set tutorials](https://docs.azure.cn/en-us/virtual-machine-scale-sets/#step-by-step-tutorials).
* Learn how to [export Azure Resource Manager templates](https://docs.azure.cn/en-us/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates).
* Review the [Azure Resource Manager overview](https://docs.azure.cn/en-us/azure-resource-manager/resource-group-overview).
* Get an overview of [virtual machine scale sets](https://docs.azure.cn/en-us/virtual-machine-scale-sets/overview).
* Read an [overview of Azure locations](https://www.azure.cn/en-us/home/features/products-by-region).
* Learn how to [redeploy a template](https://docs.azure.cn/en-us/azure-resource-manager/resource-group-template-deploy)。


## Azure Functions  
  
See **Functions** chapter of [Migrate IoT resources](./china-migration-guidance-iot.md).

## App Service
(place holder)

## Azure Kubernets Service
(place holder)
