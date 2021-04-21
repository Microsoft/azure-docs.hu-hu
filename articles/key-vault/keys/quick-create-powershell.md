---
title: Kulcs attribútumainak létrehozása és lekérése a Azure Key Vault – Azure PowerShell
description: Rövid útmutató, amely bemutatja, hogyan állíthat be és Azure Key Vault kulcsokat Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: ede01de815a65aede410fe539b6205f1f4e4fd68
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815449"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Rövid útmutató: Kulcs beállítása és lekérése a Azure Key Vault használatával Azure PowerShell

Ebben a rövid útmutatóban egy kulcstartót hoz létre a Azure Key Vault a Azure PowerShell. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. További információt a Key Vault áttekintésében [talál.](../general/overview.md) Azure PowerShell azure-erőforrások parancsokkal vagy szkriptekkel való létrehozására és kezelésére szolgál. Miután ezt befejezte, egy kulcsot fog tárolni.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, az oktatóanyaghoz Azure PowerShell modul 1.0.0-s vagy újabb verziójára lesz szükség. A `$PSVersionTable.PSVersion` verzió megkereshez írja be a következőt: . Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Kulcs hozzáadása a Key Vault

Ha kulcsokat szeretne hozzáadni a tárolóhoz, csak néhány további lépést kell tennie. Ezt a kulcsot egy alkalmazás is használni tudja. 

Írja be az alábbi parancsokat egy **ExampleKey nevű létrehozásához:**

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "ExampleKey" -Destination "Software"
```

Most már hivatkozhat erre a kulcsra, amelyet hozzáadott a Azure Key Vault az URI-ját használva. A **"https://<-unique-keyvault-name>.vault.azure.net/keys/ExampleKey"** használatával lekérte az aktuális verziót. 

A korábban tárolt kulcs megtekintése:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -KeyName "ExampleKey"
```

Most már létrehozott egy Key Vault, tárolt egy kulcsot, és lekérte azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és tárolt benne egy tanúsítványt. Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- Áttekintés a [Azure Key Vault](../general/overview.md)
- Lásd a Azure PowerShell Key Vault [parancsmagok referenciáit](/powershell/module/az.keyvault/)
- Tekintse át [a Key Vault biztonsági áttekintését](../general/security-features.md)
