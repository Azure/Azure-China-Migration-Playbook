---
title: 迁移安全资源
description: This article provides the guidance and tools on migrating security resources.
author: msfrankchen

ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 09/06/2019
ms.author: frankch

---


# 迁移安全资源

本文提供的信息可帮助您跨 Azure 区域迁移 Azure 安全资源。

## 密钥保管库

Azure 密钥保管库的某些功能无法跨 Azure 区域迁移。

### 加密密钥

您无法迁移加密密钥。在目标区域中创建新密钥，然后使用该密钥保护目标资源（例如，Azure 存储或 Azure SQL 数据库）。安全地将数据从旧区域迁移到新区域。

### 应用程序密码

应用程序密码是证书、存储帐户密钥和其他与应用程序相关的密码。在迁移期间，首先在目标 Azure 区域中创建新的密钥保管库。然后，完成以下操作之一：
* 创建新的应用程序密码。
* 读取源 Azure 区域中的当前密码，然后在新保管库中输入值。
```PowerShell
Get-AzureKeyVaultSecret -vaultname mysecrets -name Deploydefaultpw
```
有关更多信息：
* 通过完成[密钥保管库教程](https://docs.azure.cn/zh-cn/key-vault/#step-by-step-tutorials)来刷新您的知识。
* 查阅[密钥保管库概述](https://docs.azure.cn/zh-cn/key-vault/key-vault-overview)。
* 查阅[密钥保管库 PowerShell cmdlet](https://docs.microsoft.com/zh-cn/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.5.0)。


## 应用程序网关
请参阅[迁移网络资源](./china-migration-guidance-networking.md)的**应用程序网关**章节。

## VPN网关
请参阅[迁移网络资源](./china-migration-guidance-networking.md)的**VPN网关**章节。

## Azure Active Directory
(place holder)

## 安全中心
(place holder)

## Azure 信息保护
(place holder)
