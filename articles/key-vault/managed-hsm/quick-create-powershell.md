---
title: Felügyelt kulcs attribútumainak létrehozása és lekérése a Azure Key Vault – Azure PowerShell
description: Rövid útmutató, amely bemutatja, hogyan állíthat be és le egy felügyelt kulcsot Azure Key Vault a Azure PowerShell
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/26/2021
ms.topic: quickstart
ms.service: key-vault
ms.subservice: keys
tags:
- azure-resource-manager
ms.custom:
- mode-api
ms.openlocfilehash: ba1cd8d6b1410be30eefe9dca9675daaf6c16256
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534668"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Rövid útmutató: Felügyelt kulcs beállítása és lekérése Azure Key Vault PowerShell használatával

Ebben a rövid útmutatóban egy kulcstartót hoz létre a Azure Key Vault a Azure PowerShell. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. További információt a Key Vault áttekintésében [talál.](../general/overview.md) Azure PowerShell azure-erőforrások parancsokkal vagy szkriptekkel való létrehozására és kezelésére szolgál. Miután ezt befejezte, egy kulcsot fog tárolni.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, az oktatóanyaghoz Azure PowerShell modul 1.0.0-s vagy újabb verziójára lesz szükség. A `$PSVersionTable.PSVersion` verzió megkereshez írja be a következőt: . Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával hozzon létre egy *myResourceGroup* nevű erőforráscsoportot a *westus helyen.* 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

Az egyszerű azonosító formátuma a következő lesz: "xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

## <a name="create-a-managed-hsm"></a>Felügyelt HSM létrehozása

A Azure PowerShell [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) parancsmag használatával hozzon létre egy új Key Vault HSM-et. A következő információkat kell megadnia:

- Managed HSM neve: 3–24 karakter hosszúságú sztring, amely csak számokat (0–9), betűket (a-z, A-Z) és kötőjelet (-) tartalmazhat

  > [!Important]
  > Minden felügyelt HSM-nek egyedi névvel kell lennie. Cserélje <-unique-managed-hsm-name> a felügyelt HSM nevére a következő példákban.

- Erőforráscsoport neve: **myResourceGroup.**
- A hely: **EastUS**.
- A rendszerbiztonsági tag azonosítója: adja Azure Active Directory előző szakaszban lekért egyszerű rendszerbiztonsági tag azonosítóját az "Administrator" paraméternek. 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

A parancsmag kimenete az újonnan létrehozott felügyelt HSM tulajdonságait jeleníti meg. Jegyezze fel az alábbi két tulajdonságot:

- **Managed HSM Name (Felügyelt HSM** neve): A fenti --name paraméterhez megadott név.
- **Tároló** URI-ja: A példában ez &lt; https://-your-unique-managed-hsm-name &gt; .vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.

## <a name="activate-your-managed-hsm"></a>A felügyelt HSM aktiválása

A HSM aktiválásáig minden adatsíkparancs le van tiltva. Nem fog tudni kulcsokat létrehozni vagy szerepköröket hozzárendelni. A HSM-et csak a létrehozási parancs során hozzárendelt kijelölt rendszergazdák aktiválhatják. A HSM aktiválásához le kell töltenie a [biztonsági tartományt.](security-domain.md)

A HSM aktiválásához a következőre lesz szüksége:
- Legalább 3 RSA-kulcspár (legfeljebb 10)
- Adja meg a biztonsági tartomány visszafejtéséhez szükséges kulcsok minimális számát (kvórum)

A HSM aktiválásához legalább 3 (legfeljebb 10) RSA nyilvános kulcsot kell küldenie a HSM-nek. A HSM ezekkel a kulcsokkal titkosítja a biztonsági tartományt, és visszaküldi. Ha a biztonsági tartomány letöltése sikeresen befejeződött, a HSM használatra kész. Meg kell adnia a kvórumot is, amely a biztonsági tartomány visszafejtéséhez minimálisan szükséges titkos kulcsok száma.

Az alábbi példa bemutatja, hogyan hozhat létre 3 önaírt tanúsítványt a használatával (amely itt `openssl` érhető el Windows rendszeren érhető el). [](https://slproweb.com/products/Win32OpenSSL.html)

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Hozza létre és tárolja biztonságosan az ebben a lépésben létrehozott RSA-kulcspárokat és biztonságitartomány-fájlt.

Az Azure PowerShell [Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) parancsmaggal töltse le a biztonsági tartományt, és aktiválja a felügyelt HSM-et. Az alábbi példa 3 RSA-kulcspárt használ (ehhez a parancshoz csak nyilvános kulcsok szükségesek), és a kvórumot 2-re állítja.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Tárolja biztonságosan a biztonsági tartományfájlt és az RSA-kulcspárokat. Vészhelyreállításhoz vagy egy másik felügyelt HSM létrehozásához, amely ugyanazon a biztonsági tartományon osztozik, szüksége lesz rájuk a kulcsok megosztásához.

A biztonsági tartomány sikeres letöltése után a HSM aktív állapotban lesz, és készen áll a használatra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és tárolt benne egy tanúsítványt. Ha többet szeretne megtudni a Key Vault és az alkalmazásokba való integrálásáról, folytassa az alábbi cikkekkel.

- Olvassa el [a Azure Key Vault](../general/overview.md)
- Lásd a Azure PowerShell Key Vault [parancsmagok referenciáit](/powershell/module/az.keyvault/)
- Tekintse át [a Key Vault biztonsági áttekintését](../general/security-overview.md)
