---
title: Migrate security resources
description: This article provides the guidance and tools on migrating security resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/26/2019
ms.author: frankch

---


# Migrate Security Resources

This article has information that you may find helpful as you migrate Azure security resources across Azure regions.

## Key Vault

Some features of Azure Key Vault can’t be migrated across Azure regions.

### Encryption keys

You can’t migrate encryption keys. Create new keys in the target region, and then use the keys to protect the target resource (for example, Azure Storage or Azure SQL Database). Securely migrate the data from the old region to the new region.

### Application secrets

Application secrets are certificates, storage account keys, and other application-related secrets. During a migration, first create a new key vault in the target Azure region. Then, complete one of the following actions:

* Create new application secrets.
* Read the current secrets in the source Azure region, and then enter the value in the new vault.
```PowerShell
Get-AzureKeyVaultSecret -vaultname mysecrets -name Deploydefaultpw
```
For more information: 
* Refresh your knowledge by completing the [Key Vault tutorials.](https://docs.azure.cn/key-vault/#step-by-step-tutorials)
* Review the [Key Vault overview.](https://docs.azure.cn/key-vault/key-vault-overview)
* Review the [Key Vault PowerShell cmdlets.](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.5.0)


## Application Gateway

See **Application Gateway** chapter of [Migrate Network Resources](./china-migration-guidance-networking.md).

## VPN Gateway

See **VPN Gateway** chapter of [Migrate Network Resources](./china-migration-guidance-networking.md).

## Azure Active Directory  

Azure AD function has no regional properties and no migration is required.


## Azure Information Protection  

Azure Information Protection function has no regional properties and no migration is required.

## Security Center

Security Center function has no regional properties and no migration is required.
