---
title: Hozzáférés-Azure Key Vault hozzárendelése
description: A Azure Portal, az Azure CLI vagy Azure PowerShell használata Key Vault hozzáférési szabályzatok rendszerbiztonsági taghoz vagy alkalmazásidentitáshoz való hozzárendelésére.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 1c7c31f38d6a59f4ded17e1e1fd7e985ce59922a
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751417"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Hozzáférés-Key Vault hozzárendelése a Azure PowerShell

A Key Vault házirend határozza meg, hogy egy adott rendszerbiztonsági tag, azaz felhasználó, alkalmazás vagy felhasználói [](../secrets/index.yml)csoport [](../keys/index.yml)különböző műveleteket hajthat-e végre Key Vault titkos kulcsokon, kulcsokon és [tanúsítványokon.](../certificates/index.yml) Hozzáférési szabályzatokat a [Azure Portal,](assign-access-policy-portal.md)az [Azure CLI](assign-access-policy-cli.md)vagy Azure PowerShell (ez a cikk) használatával rendelhet hozzá.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

További információ a csoportok létrehozásáról a Azure Active Directory a Azure PowerShell: [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) és [Add-AzADGroupMember.](/powershell/module/az.resources/add-azadgroupmember)

## <a name="configure-powershell-and-sign-in"></a>A PowerShell és a bejelentkezés konfigurálása

1. A parancsok helyi futtatásához telepítse a [Azure PowerShell,](/powershell/azure/) ha még nem.

    A parancsok közvetlenül a felhőben való futtatásához használja a [Azure Cloud Shell.](../../cloud-shell/overview.md)

1. Csak helyi PowerShell esetén:

    1. Telepítse a [Azure Active Directory PowerShell-modult.](https://www.powershellgallery.com/packages/AzureAD)

    1. Jelentkezzen be az Azure-ba:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Az objektumazonosító lekérte

Határozza meg annak az alkalmazásnak, csoportnak vagy felhasználónak az objektumazonosítóját, amelyhez hozzá szeretné rendelni a hozzáférési szabályzatot:

- Alkalmazások és egyéb szolgáltatásnév: használja a [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) parancsmagot a paraméterrel az eredmények a kívánt szolgáltatásnév `-SearchString` nevére való szűréséhez:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Csoportok: használja a [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) parancsmagot a paraméterrel az eredmények a kívánt csoport `-SearchString` nevére való szűréséhez:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    A kimenetben az objektumazonosító a következőként szerepel: `Id` .

- Felhasználók: használja a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) parancsmagot, és továbbadja a felhasználó e-mail-címét a `-UserPrincipalName` paraméternek.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    A kimenetben az objektumazonosító a következőként szerepel: `Id` .

## <a name="assign-the-access-policy"></a>A hozzáférési szabályzat hozzárendelése

Használja a [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmagot a hozzáférési szabályzat hozzárendelésére:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

Csak a , a és a típust kell tartalmaznia, amikor `-PermissionsToSecrets` `-PermissionsToKeys` `-PermissionsToCertificates` engedélyeket rendel ezekhez a típusokhoz. A , és megengedett értékei `<secret-permissions>` `<key-permissions>` a `<certificate-permissions>` [Set-AzKeyVaultAccessPolicy – Parameters dokumentációban vannak megadva.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters)

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault biztonság: Identitás- és hozzáférés-kezelés](security-overview.md#identity-management)
- [Kulcstartó biztonságossá való tere.](security-overview.md)
- [Azure Key Vault fejlesztői útmutató](developers-guide.md)