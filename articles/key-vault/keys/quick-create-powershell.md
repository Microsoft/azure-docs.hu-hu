---
title: Kulcsok attribútumainak létrehozása és lekérése Azure Key Vaultban – Azure PowerShell
description: Gyors útmutató, amely bemutatja, hogyan állíthatja be és kérhet le egy kulcsot a Azure Key Vault használatával Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: adbf3080367e54147c981c8ccf0bb6236111b8c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99071205"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Gyors útmutató: kulcs beállítása és lekérése Azure Key Vault használatával Azure PowerShell

Ebben a rövid útmutatóban egy kulcstartót hoz létre Azure Key Vaultban Azure PowerShell. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. További információ a Key Vaultről: [Áttekintés](../general/overview.md). Azure PowerShell az Azure-erőforrások parancsok vagy parancsfájlok használatával történő létrehozására és kezelésére szolgál. Ha ezt elvégezte, egy kulcsot fog tárolni.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz a Azure PowerShell-modul 1.0.0-as vagy újabb verziójára lesz szükség. `$PSVersionTable.PSVersion`A verzió megkereséséhez írja be a következőt:. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Kulcs hozzáadása a Key Vaulthoz

A kulcs a tárolóhoz való hozzáadásához mindössze néhány további lépést kell elvégeznie. Ezt a kulcsot egy alkalmazás használhatja. 

A nevezett **ExampleKey** létrehozásához írja be az alábbi parancsokat:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "ExampleKey" -Destination "Software"
```

Most már hivatkozhat erre a kulcsra, amelyet a Azure Key Vaulthoz adott hozzá az URI használatával. A jelenlegi verziójának lekéréséhez használja a **"https://<Your-Unique-kulcstartó-name>. Vault.Azure.net/Keys/ExampleKey"** lehetőséget. 

A korábban tárolt kulcs megtekintése:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -KeyName "ExampleKey"
```

Most létrehozott egy Key Vault, tárolt egy kulcsot, és beolvasta azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és tárolt benne egy tanúsítványt. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse meg a [Azure PowerShell Key Vault-parancsmagok](/powershell/module/az.keyvault/) referenciáját
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)
