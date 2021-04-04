---
title: Azure Key Vault hozzáférési szabályzat kiosztása
description: Az Azure Portal, az Azure CLI vagy a Azure PowerShell használatával Key Vault hozzáférési szabályzatot rendelhet hozzá egy egyszerű szolgáltatáshoz vagy alkalmazás identitásához.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 4cf0393cdb0aae1b352b81df4893f874f34970cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934533"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Key Vault hozzáférési szabályzat kiosztása a Azure PowerShell használatával

A Key Vault hozzáférési szabályzat meghatározza, hogy egy adott szolgáltatásnév, azaz egy alkalmazás vagy felhasználói csoport különböző műveleteket hajthat végre Key Vault [titkokon](../secrets/index.yml), [kulcsokon](../keys/index.yml)és [tanúsítványokon](../certificates/index.yml). Hozzáférési szabályzatokat a [Azure Portal](assign-access-policy-portal.md), az [Azure CLI](assign-access-policy-cli.md)vagy a Azure PowerShell használatával rendelhet hozzá (ez a cikk).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

A csoportok Azure Active Directory Azure PowerShell használatával történő létrehozásával kapcsolatos további információkért lásd: [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) és [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>A PowerShell és a bejelentkezés konfigurálása

1. A parancsok helyi futtatásához telepítse a [Azure PowerShell](/powershell/azure/) , ha még nem tette meg.

    Ha közvetlenül a felhőben szeretné futtatni a parancsokat, használja a [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Csak helyi PowerShell:

    1. Telepítse a [Azure Active Directory PowerShell-modult](https://www.powershellgallery.com/packages/AzureAD).

    1. Jelentkezzen be az Azure-ba:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Az objektumazonosító beszerzése

Határozza meg annak az alkalmazásnak, csoportnak vagy felhasználónak az AZONOSÍTÓját, amelyhez hozzá szeretné rendelni a hozzáférési házirendet:

- Alkalmazások és egyéb egyszerű szolgáltatások: a [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) parancsmaggal a `-SearchString` paraméterrel szűrheti az eredményeket a kívánt egyszerű szolgáltatásnév nevével:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Csoportok: használja a [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) parancsmagot a `-SearchString` paraméterrel az eredmények szűréséhez a kívánt csoport nevére:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    A kimenetben az objektum azonosítója a következőképpen jelenik meg: `Id` .

- Felhasználók: használja a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) parancsmagot, és adja át a felhasználó e-mail-címét a `-UserPrincipalName` paraméternek.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    A kimenetben az objektum azonosítója a következőképpen jelenik meg: `Id` .

## <a name="assign-the-access-policy"></a>Hozzáférési házirend kiosztása

A hozzáférési házirend hozzárendeléséhez használja a [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmagot:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

`-PermissionsToSecrets` `-PermissionsToKeys` Az adott típusokhoz csak a, a és az `-PermissionsToCertificates` engedélyek kiosztása szükséges. A () és a () számára engedélyezett értékek a `<secret-permissions>` `<key-permissions>` `<certificate-permissions>` [set-AzKeyVaultAccessPolicy-Parameters](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) dokumentációban vannak megadva.

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault biztonság: identitás-és hozzáférés-kezelés](security-overview.md#identity-management)
- [A kulcstartó védelme](secure-your-key-vault.md).
- [Azure Key Vault fejlesztői útmutató](developers-guide.md)