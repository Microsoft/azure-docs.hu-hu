---
title: Többsoros titok tárolása Azure Key Vault
description: 'Oktatóanyag: többsoros titok beállítása Azure Key Vault az Azure CLI-vel és a PowerShell-lel'
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: e320795d5e8623dea9b97ac6371a0ffc6e6117f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610284"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>Többsoros titok tárolása Azure Key Vault

Az [Azure CLI](quick-create-cli.md) gyors üzembe helyezési útmutatója és [Azure PowerShell](quick-create-powershell.md) rövid útmutató bemutatja, hogyan tárolhat egy egysoros titkot.   A Key Vault használatával többsoros titkot is tárolhat, például egy JSON-fájlt vagy egy RSA titkos kulcsot.

A többsoros titkok nem adhatók át az Azure CLI az kulcstartó [Secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) parancs vagy a Azure PowerShell [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) parancsmag segítségével a parancssorban. Ehelyett a többsoros titkot szövegfájlként kell tárolnia. 

Létrehozhat például egy "secretfile.txt" nevű szövegfájlt, amely a következő sorokat tartalmazza:

```bash
This is my
multi-line
secret
```

Ezt követően a paraméter használatával átadhatja ezt a fájlt az Azure CLI az kulcstartó [Secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) paranccsal `--file` .

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

A Azure PowerShell használatával először olvassa el a fájlt a [Get-Content](/powershell/module/microsoft.powershell.management/get-content) parancsmaggal, majd alakítsa át biztonságos karakterlánccá a [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)használatával. 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

Végül tárolja a titkot a [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) parancsmag használatával.

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

Mindkét esetben megtekintheti a tárolt titkos kulcsot az Azure CLI az kulcstartó [Secret show](/cli/azure/keyvault/secret#az_keyvault_secret_show) parancs vagy a Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) parancsmag használatával.

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

A titkos sortörések a következővel lesznek visszaadva:

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>Következő lépések

- [A Azure Key Vault áttekintése](../general/overview.md)
- Az [Azure CLI](quick-create-cli.md) rövid útmutatója
- Lásd az [Azure CLI az kulcstartó parancsait](/cli/azure/keyvault)
- Tekintse meg a [Azure PowerShell](quick-create-powershell.md) rövid útmutatót
- Lásd: [Azure PowerShell az. kulcstartó parancsmagok](/powershell/module/az.keyvault#key-vault)
