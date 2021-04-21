---
title: Ügyfélkódtárak Azure Key Vault | Microsoft Docs
description: Ügyfélkódtárak Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: b8690b5ce3e2aee9a554d3e0682161b86264e124
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818532"
---
# <a name="client-libraries-for-azure-key-vault"></a>Ügyfélkódtárak Azure Key Vault

Az ügyféloldali kódtárak Azure Key Vault lehetővé teszik a Key Vault különböző nyelvek, például a .NET, a Python, a Java és a JavaScript programnyelvi funkcióihoz való programozott hozzáférést.

## <a name="client-libraries-per-language-and-object"></a>Ügyfélkódtárak nyelvenként és objektumonként

Minden SDK külön ügyfélkódtárakkal rendelkezik a kulcstartóhoz, a titkos kulcsokhoz, a kulcsokhoz és a tanúsítványokhoz az alábbi táblázat szerint.

| Nyelv | Titkos kulcsok | Kulcsok | Tanúsítványok | Key Vault (felügyeleti sík) |
|--|--|--|--|--|
| .NET | - [API-referencia](/dotnet/api/azure.security.keyvault.secrets)<br>- [NuGet-csomag](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Quickstart](../secrets/quick-create-net.md) | - [API-referencia](/dotnet/api/azure.security.keyvault.keys)<br>- [NuGet-csomag](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [Quickstart](../keys/quick-create-net.md) | - [API-referencia](/dotnet/api/azure.security.keyvault.certificates)<br>- [NuGet-csomag](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [Quickstart](../certificates/quick-create-net.md) | - [API-referencia](/dotnet/api/microsoft.azure.management.keyvault)<br>- [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [API-referencia](/python/api/overview/azure/keyvault-secrets-readme)<br>- [PyPi-csomag](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-python.md) |- [API-referencia](/python/api/overview/azure/keyvault-keys-readme)<br>- [PyPi-csomag](https://pypi.org/project/azure-keyvault-keys/)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Quickstart](../keys/quick-create-python.md) | - [API-referencia](/python/api/overview/azure/keyvault-certificates-readme)<br>- [PyPi-csomag](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-python.md) | - [API-referencia](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br> - [PyPi-csomag](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [API-referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-java.md) |- [API-referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [Quickstart](../keys/quick-create-java.md) | - [API-referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-java.md) |- [API-referencia](/java/api/com.microsoft.azure.management.keyvault)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [API-referencia](/javascript/api/@azure/keyvault-secrets/)<br>- [npm-csomag](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-node.md) |- [API-referencia](/javascript/api/@azure/keyvault-keys/)<br>- [npm-csomag](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [Quickstart](../keys/quick-create-node.md)| - [API-referencia](/javascript/api/@azure/keyvault-certificates/)<br>- [npm-csomag](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-node.md) |  - [API-referencia](/javascript/api/@azure/arm-keyvault/)<br>- [npm-csomag](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Következő lépések

- Lásd a [Azure Key Vault fejlesztői útmutatót](developers-guide.md)
- További információ a Key [Vaultban való hitelesítésről](authentication.md)
- További információ: [Biztonságos hozzáférés biztosítása egy Key Vault](security-features.md)
