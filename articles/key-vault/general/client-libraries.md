---
title: Azure Key Vault ügyféloldali kódtárai | Microsoft Docs
description: Azure Key Vault ügyféloldali kódtárai
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: c5d13c49b4d3f65901305d9b29ca0b2576e91e86
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455365"
---
# <a name="client-libraries-for-azure-key-vault"></a>Azure Key Vault ügyféloldali kódtárai

A Azure Key Vault-hez készült ügyféloldali kódtárak különböző nyelveken, többek között a .NET, a Python, a Java és a JavaScript használatával is lehetővé teszik a programozott hozzáférést a Key Vault funkciókhoz.

## <a name="client-libraries-per-language-and-object"></a>Ügyféloldali kódtárak nyelv és objektum alapján

Minden SDK külön ügyféloldali kódtárakat tartalmaz a Key vaulthoz, a titkokhoz, a kulcsokhoz és a tanúsítványokhoz az alábbi táblázatban.

| Nyelv | Titkos kulcsok | Kulcsok | Tanúsítványok | Key Vault (felügyeleti sík) |
|--|--|--|--|--|
| .NET | - [API-referenciák](/dotnet/api/azure.security.keyvault.secrets)<br>- [NuGet-csomag](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [QuickStart](../secrets/quick-create-net.md) | - [API-referenciák](/dotnet/api/azure.security.keyvault.keys)<br>- [NuGet-csomag](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [QuickStart](../keys/quick-create-net.md) | - [API-referenciák](/dotnet/api/azure.security.keyvault.certificates)<br>- [NuGet-csomag](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [QuickStart](../certificates/quick-create-net.md) | - [API-referenciák](/dotnet/api/microsoft.azure.management.keyvault)<br>- [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [API-referenciák](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [PyPi-csomag](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [QuickStart](../secrets/quick-create-python.md) |- [API-referenciák](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [PyPi-csomag](https://pypi.org/project/azure-keyvault-keys/)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [QuickStart](../keys/quick-create-python.md) | - [API-referenciák](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [PyPi-csomag](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [QuickStart](../certificates/quick-create-python.md) | - [API-referenciák](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault?view=azure-python)<br> - [PyPi-csomag](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [API-referenciák](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [QuickStart](../secrets/quick-create-java.md) |- [API-referenciák](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [QuickStart](../keys/quick-create-java.md) | - [API-referenciák](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [QuickStart](../certificates/quick-create-java.md) |- [API-referenciák](/java/api/com.microsoft.azure.management.keyvault)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [API-referenciák](/javascript/api/@azure/keyvault-secrets/)<br>- [NPM-csomag](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [QuickStart](../secrets/quick-create-node.md) |- [API-referenciák](/javascript/api/@azure/keyvault-keys/)<br>- [NPM-csomag](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [QuickStart](../keys/quick-create-node.md)| - [API-referenciák](/javascript/api/@azure/keyvault-certificates/)<br>- [NPM-csomag](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [QuickStart](../certificates/quick-create-node.md) |  - [API-referenciák](/javascript/api/@azure/arm-keyvault/)<br>- [NPM-csomag](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Következő lépések

- Lásd: [Azure Key Vault fejlesztői útmutató](developers-guide.md)
- További információ a [Key Vault hitelesítéséről](authentication.md)
- További információ [a Key Vault való hozzáférés biztonságossá tételéről](secure-your-key-vault.md)
