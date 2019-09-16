---
title: Migrate IoT resources to the target Azure region
description: This article provides the guidance and tools on migrating IoT to target Azure region.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/16/2019
ms.author: frankch

---


# Migrate IoT resources to the target Azure region

This section contains information that you may find helpful as you migrate Azure IoT resources across Azure regions.
 
## Functions

Migrating Azure Functions resources from across Azure regions isn’t supported at this time. We recommend that you export a Resource Manager template, change the location, and then redeploy to the target region.

>*IMPORTANT*  
>*Change location, Azure Key Vault secrets, certificates, and other GUIDs to be consistent with the new region.*

For more information:
* Refresh your knowledge by completing the [Functions tutorials](https://docs.azure.cn/azure-functions/#step-by-step-tutorials).
* Learn how to [export Resource Manager templates](https://docs.azure.cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates) or read the overview of [Azure Resource Manager](https://docs.azure.cn/azure-resource-manager/resource-group-overview).
* Review the [Azure Functions overview](https://docs.azure.cn/azure-functions/functions-overview).
* Learn how to [redeploy a template](https://docs.azure.cn/azure-resource-manager/resource-group-template-deploy).

## Notification Hubs

To migrate settings from one instance of Azure Notification Hubs to another instance, export and then import all registration tokens and tags:
1. [Export the existing notification hub registrations] to an Azure Blob storage container(https://docs.azure.cn/notification-hubs/export-modify-registrations-bulk#export).
2.	Create a new notification hub in the target environment.
3. [Import your registration tokens](https://docs.azure.cn/zh-cn/notification-hubs/export-modify-registrations-bulk#import) from Blob storage to your new notification hub.  

For more information: 
* Refresh your knowledge by completing the [Notification Hubs tutorials](https://docs.azure.cn/notification-hubs/#step-by-step-tutorials).
* Review the [Notification Hubs overview](https://docs.azure.cn/notification-hubs/notification-hubs-push-notification-overview).

## IoT Hub

To migrate IoT Hub, re-create the IoT Hub and use the export/import device identities function:

>*NOTE*  
>*This migration might cause downtime and data loss in your Azure IoT application. All telemetry messages, C2D commands, and job-related information (schedules and history) aren't migrated. You must reconfigure your devices and back-end applications to start using the new connection strings.*

### Step 1: Re-create the IoT hub

IoT Hub doesn’t support cloning natively. However, you can use the Azure Resource Manager feature to [export a resource group as a template](https://docs.azure.cn/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates) to export your IoT Hub metadata. Configured routes and other IoT hub settings are included in the exported metadata. Then, redeploy the template in global Azure. You might find it easier to re- create the IoT hub in the Azure portal by looking at the details in the exported JSON.

### Step 2: Migrate device identities
To migrate device identities:
1. In the source tenant, use the [ExportDevices](https://docs.azure.cn/zh-cn/iot-hub/iot-hub-bulk-identity-mgmt) Resource Manager API to export all device identities, device twins, and module twins (including the keys) to a storage container. You can use a storage container in either the source or the target Azure region. Make sure that the generated shared access signature URI has sufficient permissions.

2. Run the [ImportDevices](https://docs.azure.cn/en-us/iot-hub/iot-hub-bulk-identity-mgmt) Resource Manager API to import all device identities from the storage container to the cloned IoT hub in the target Azure region.

3. Reconfigure your devices and back-end services to start using the new connection strings from the new IoT hub created in Step 1.

For more information: 
* Learn how to [export IoT Hub bulk identities](https://docs.azure.cn/iot-hub/iot-hub-bulk-identity-mgmt#export-devices).
* Learn how to [import IoT Hub bulk identities](https://docs.azure.cn/iot-hub/iot-hub-bulk-identity-mgmt#import-devices).
* Review the [Azure IoT Hub overview](https://docs.azure.cn/iot-hub/about-iot-hub).


## Event Hubs

See **Event Hubs** chapter of [Migrate analtyics Services](./china-migration-guidance-analytics.md).

## Stream Analtyics

See **Stream Analtyics** chapter of [Migrate analtyics Services](./china-migration-guidance-analytics.md).

## Logic Apps

See **Logic Apps** chapter of [Migrate integration resources to the target Azure region](./china-migration-guidance-integration.md).

## API Management

See **API Management** chapter of [Migrate web resources](./china-migration-guidance-web.md).

## Azure Cosmos DB

See **Azure Cosmos DB** chapter of [Migrate database resources](./china-migration-guidance-database.md).

## Azure  Time Series Insights
(place holder)

## Event Grid

See **Event Grid** chapter of [Migrate integration resources](./china-migration-guidance-integration.md).


 
