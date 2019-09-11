---
title: Migration Process
description: This article describes four phases and detailed steps to migrate resources between different Azure China regions.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/09/2019
ms.author: frankch

---

# Migration Process

This document provides guidance that you may find helpful as you migrate your workloads from one Azure region to another.
While planning the migration of a workload, the focus should be on the application as a migration entity. Once the migration planning is complete, the required Azure resources can then be migrated one at a time.
The steps in the migration process are as follows:

![迁移过程](../migrate/media/china-migration-process/process.jpg)

## Assess

It’s important to understand your organization’s footprint you’re looking to migrate by bringing together Azure account owners, subscription admins, tenant admins, and finance and accounting teams. The people who work in these roles can provide a complete picture of Azure usage for a large organization.

In the assessment stage, compile an inventory of resources: 
*	Each subscription admin and tenant admin should run a series of scripts to list resource groups, the resources in each resource group, and resource group deployment settings in the environment.
*	Dependencies across applications in Azure and with external systems should be documented.
* The count of each Azure resource and the amount of data that are associated with each instance you want to migrate should be documented.
* Ensure that application architecture documents are consistent with the Azure resources list.

At the end of this stage, you’ll have:
* A complete list of Azure resources that need to be migrated.
* A list of dependencies between resources.
* Information about the complexity of the migration effort.
 
## Plan

In the planning stage, you should complete the following tasks:：
* Use the output of the dependency analysis completed in the assessment stage to define related components. Consider migrating related components together in a *migration package*.
* (Optional) Use the migration as an opportunity to apply [**Gartner 5-R criteria**](https://www.gartner.com/newsroom/id/1684114) and to optimize your workload. 
* Determine the target environment in the target Azure region.
* Identify the target Azure tenant (create one, if necessary).
* Create subscriptions.
* Choose the target Azure region.
* Execute test migration scenarios that match your architecture in the source Azure region with the architecture in the target region.
* Determine the appropriate timeline and schedule for migration. Create a user acceptance test plan for each migration package.

## Migrate

In the migration phase, use the tools, techniques, and recommendations discussed in the following sections to migrate or create new resources in the target region. Then, configure applications.

## Validate

In the validation stage, complete the following tasks: 
1.	Complete user acceptance testing.
2.	Ensure that applications are working as expected.
3.	Sync the latest data to the target environment, if applicable.
4.	Switch to a new application instance in the target region.
5.	Verify that the production environment is working as expected.
6.	Decommission resources in the source region. 

 
## Terms

These terms are used in the following sections:

 **Source** describes where you’re migrating resources from:
* **Source tenant name**：The name of the tenant in the source Azure region (everything after @ in the account name).
* **Source tenant ID**：The ID of the tenant in the source Azure region. The tenant ID appears in the Azure portal when you move the mouse over the account name in the upper-right corner.
* **Source subscription ID**：The ID of the resource subscription in the source Azure region. You can have more than one subscription in the same tenant. Always make sure that you’re using the correct subscription.
* **Source region**：The region where the resource you want to migrate is located.

 **Target** or **destination**describes where you’re migrating resources to:
* **Target tenant name**：The name of the tenant in the target Azure region.
* **Target tenant ID**：The ID of the tenant in the target region.
* **Target subscription ID**：The subscription ID for the resource in the target region.
* **Target region**：The region where you want to migrate your resource to.

>*NOTE*   
>*Verify that the Azure service you’re migrating is offered in the target region.*
