---
title: Felügyelt kulcs attribútumainak létrehozása és lekérése Azure Key Vaultban – Azure PowerShell
description: Gyors útmutató, amely bemutatja, hogyan lehet felügyelt kulcsot beállítani és beolvasni Azure Key Vault a Azure PowerShell használatával
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/26/2021
ms.author: mbaldwin
ms.openlocfilehash: 943555e9f7a26530a075aee27dd310d96974e652
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072913"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Gyors útmutató: felügyelt kulcs beállítása és lekérése Azure Key Vaultról a PowerShell használatával

Ebben a rövid útmutatóban egy kulcstartót hoz létre Azure Key Vaultban Azure PowerShell. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. További információ a Key Vaultről: [Áttekintés](../general/overview.md). Azure PowerShell az Azure-erőforrások parancsok vagy parancsfájlok használatával történő létrehozására és kezelésére szolgál. Ha ezt elvégezte, egy kulcsot fog tárolni.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz a Azure PowerShell-modul 1.0.0-as vagy újabb verziójára lesz szükség. `$PSVersionTable.PSVersion`A verzió megkereséséhez írja be a következőt:. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával hozzon létre egy *myResourceGroup* nevű erőforráscsoportot a *westus* helyen. 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

A résztvevő AZONOSÍTÓját a rendszer a következő formátumban adja vissza: "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX".

## <a name="create-a-managed-hsm"></a>Felügyelt HSM létrehozása

Új Key Vault Managed HSM létrehozásához használja a Azure PowerShell [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) parancsmagot. A következő információkat kell megadnia:

- Felügyelt HSM neve: 3 – 24 karakterből álló karakterlánc, amely csak számokat (0-9), betűket (a-z, A-Z) és kötőjeleket (-) tartalmazhat.

  > [!Important]
  > Minden felügyelt HSM egyedi névvel kell rendelkeznie. A következő példákban cserélje le <az-egyedi-felügyelt-HSM-Name> a felügyelt HSM nevére.

- Erőforráscsoport neve: **myResourceGroup**.
- A hely: **EastUS**.
- A résztvevő azonosítója: adja át az utolsó szakaszban beszerzett Azure Active Directory rendszerbiztonsági tag AZONOSÍTÓját a "rendszergazda" paraméternek. 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

A parancsmag kimenete az újonnan létrehozott felügyelt HSM tulajdonságait jeleníti meg. Jegyezze fel az alábbi két tulajdonságot:

- **Felügyelt HSM neve**: a fenti--Name paraméterhez megadott név.
- Tároló **URI-ja**: a példában ez a https:// &lt; a-Unique-Managed-HSM-name &gt; . Vault.Azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.

## <a name="activate-your-managed-hsm"></a>A felügyelt HSM aktiválása

Az összes adatsík-parancs le van tiltva, amíg be nem aktiválja a HSM-et. A kulcsok nem hozhatók létre és nem rendelhetők hozzá szerepkörök. Csak a létrehozási parancs során hozzárendelt kijelölt rendszergazdák aktiválják a HSM-et. A HSM aktiválásához le kell töltenie a [biztonsági tartományt](security-domain.md).

A HSM aktiválásához a következőkre lesz szüksége:
- Minimum 3 RSA-kulcs – pár (legfeljebb 10)
- A biztonsági tartomány (kvórum) visszafejtéséhez szükséges kulcsok minimális számának megadása

A HSM aktiválásához legalább 3 (legfeljebb 10) RSA nyilvános kulcsot kell küldenie a HSM-nek. A HSM titkosítja a biztonsági tartományt ezekkel a kulcsokkal, és visszaküldi azt. Miután a biztonsági tartomány letöltése sikeresen befejeződött, a HSM készen áll a használatra. Meg kell adnia a kvórumot is, amely a biztonsági tartomány visszafejtéséhez szükséges titkos kulcsok minimális száma.

Az alábbi példa azt mutatja be, hogyan használható `openssl` (a [](https://slproweb.com/products/Win32OpenSSL.html)Windows számára elérhető) a 3 önaláírt tanúsítvány létrehozásához.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Hozza létre és tárolja az ebben a lépésben létrehozott RSA-kulcspár és biztonsági tartományi fájl biztonságos tartományát.

Használja a Azure PowerShell [export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) parancsmagot a biztonsági tartomány letöltéséhez és a felügyelt HSM aktiválásához. Az alábbi példa 3 RSA-kulcspárt használ (csak a parancshoz tartozó nyilvános kulcsok szükségesek), és a kvórumot a 2 értékre állítja.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

A biztonsági tartományi fájlt és az RSA-kulcs párokat biztonságosan tárolja. Szüksége lesz rájuk a vész-helyreállításhoz vagy egy olyan felügyelt HSM létrehozásához, amely azonos biztonsági tartománnyal osztozik, így a kulcsok megoszthatók.

A biztonsági tartomány sikeres letöltése után a HSM aktív állapotban lesz, és készen áll a használatra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és tárolt benne egy tanúsítványt. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse meg a [Azure PowerShell Key Vault-parancsmagok](/powershell/module/az.keyvault/) referenciáját
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)
