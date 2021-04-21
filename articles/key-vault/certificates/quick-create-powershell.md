---
title: Rövid útmutató – Tanúsítványok & nézetének Azure Key Vault beállítása Azure PowerShell
description: Rövid útmutató, amely bemutatja, hogyan állíthat be és Azure Key Vault tanúsítványt Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: a641ca1206cb41ded0513db72daa278dc3753c85
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750391"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>Rövid útmutató: Tanúsítvány beállítása és lekérése a Azure Key Vault a Azure PowerShell

Ebben a rövid útmutatóban egy kulcstartót hoz létre a Azure Key Vault a Azure PowerShell. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. További információt a Key Vault áttekintésében [talál.](../general/overview.md) Azure PowerShell azure-erőforrások parancsokkal vagy szkriptekkel való létrehozására és kezelésére szolgál. Miután ezt befejezte, egy tanúsítványt fog tárolni.

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

## <a name="add-a-certificate-to-key-vault"></a>Tanúsítvány hozzáadása a Key Vault

Ha tanúsítványt szeretne hozzáadni a tárolóhoz, csak néhány további lépést kell tennie. Ezt a tanúsítványt egy alkalmazás is tudná használni. 

Írja be az alábbi parancsokat egy **exampleCertificate** nevű szabályzattal létrehozott önaírt tanúsítvány létrehozásához:

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal

Add-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Most már hivatkozhat erre a tanúsítványra, amelyet a Azure Key Vault URI-ját használva. A **"https://<-unique-keyvault-name>.vault.azure.net/certificates/ExampleCertificate"** használatával lekérte az aktuális verziót. 

A korábban tárolt tanúsítvány megtekintése:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate"
```

Létrehozott egy új tanúsítványt Key Vault tárolt egy tanúsítványt, és lekérte azt.

**Hibaelhárítás:**

A művelet érvénytelen állapotkódot adott vissza (Tiltott)

Ha ezt a hibaüzenetet kapja, a Azure Key Vault hozzáféréssel rendelkező fiók nem rendelkezik a megfelelő engedélyekkel a tanúsítványok létrehozásához.

Futtassa a Azure PowerShell parancsot a megfelelő engedélyek hozzárendelése érdekében:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> -ObjectId <AzureObjectID> -PermissionsToCertificates get,list,update,create
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és tárolt benne egy tanúsítványt. Ha többet szeretne megtudni a Key Vault és az alkalmazásokba való integrálásáról, folytassa az alábbi cikkekkel.

- Olvassa el [a Azure Key Vault](../general/overview.md)
- Lásd a Azure PowerShell Key Vault [parancsmagok referenciáit](/powershell/module/az.keyvault/)
- Tekintse át [a Key Vault biztonsági áttekintését](../general/security-overview.md)
