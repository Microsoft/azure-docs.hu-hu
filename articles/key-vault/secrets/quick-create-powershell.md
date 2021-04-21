---
title: Rövid útmutató – Titkos & lekérésének beállítása Key Vault PowerShell használatával"
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, olvashat be és törölhet titkos Azure Key Vault egy Azure PowerShell.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 8a0ebfc90fb57a6e67d7c9e41b78d9db502b2720
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814639"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Rövid útmutató: Titkos kulcs beállítása és lekérése az Azure Key Vaultból a PowerShell használatával

Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. A Key Vaulttal kapcsolatos további információt az [Áttekintés](../general/overview.md) szakaszban talál. Ebben a rövid útmutatóban egy kulcstartót hoz létre a PowerShell használatával. Ezután titkos kulcsokat tárolhat az újonnan létrehozott tárolóban.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, az oktatóanyaghoz Azure PowerShell modul 5.0.0-s vagy újabb verziójára lesz szükség. A `$PSVersionTable.PSVersion` verzió megkereshez írja be a következőt: . Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>Adjon engedélyt a felhasználói fióknak a titkos kulcsok kezeléséhez a Key Vault

A Azure PowerShell Set-AzKeyVaultAccessPolicy parancsmaggal frissítheti a Key Vault hozzáférési szabályzatot, és titkos engedélyeket adhat a felhasználói fiókjának.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -UserPrincipalName "user@domain.com" -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Titkos kulcs a tárolóhoz való hozzáadásához csak néhány lépést kell végrehajtania. Ebben az esetben egy alkalmazás által használható jelszót fog megadni. A jelszó neve **ExamplePassword,** és a **hVFkk965BuUv** értékét tárolja.

Először konvertálja a **hVFkk965BuUv** értékét biztonságos sztringgé a következő beírásával:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString "hVFkk965BuUv" -AsPlainText -Force
```

Ezután a [Azure PowerShell Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) parancsmaggal hozzon létre egy **ExamplePassword** nevű titkos kulcsot Key Vault **hVFkk965BuUv értékkel:**


```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword" -SecretValue $secretvalue
```

## <a name="retrieve-a-secret-from-key-vault"></a>Titkos adat lekérése a Key Vault

A titkos kódban tárolt érték megtekintése egyszerű szövegként:

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword"
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Most létrehozott egy Key Vaultot, tárolt egy titkos kulcsot, és lekérte azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

 A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot, a Key Vault és az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault és tárolt benne egy titkos adatokat. Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- Áttekintés a [Azure Key Vault](../general/overview.md)
- Megtudhatja, hogyan [tárolható többsoros titkos kulcsok Key Vault](multiline-secrets.md)
- Lásd a Azure PowerShell Key Vault [parancsmagok referenciáit](/powershell/module/az.keyvault/#key_vault)
- A biztonsági [Key Vault áttekintése](../general/security-features.md)
